# Circular Buffer (Ring Buffer)

Last Updated: 2025-01-14

## Overview
A Circular Buffer is a fixed-size buffer that wraps around to the beginning when full. It's particularly useful for streaming data and implementing queues with fixed memory.

## Properties
1. Fixed size
2. FIFO behavior
3. Constant time operations
4. Memory efficient
5. No memory fragmentation

## Basic Implementation

### Simple Circular Buffer
```python
class CircularBuffer:
    def __init__(self, size):
        self.size = size
        self.buffer = [None] * size
        self.head = 0  # Read position
        self.tail = 0  # Write position
        self.count = 0  # Number of elements
    
    def is_empty(self):
        return self.count == 0
    
    def is_full(self):
        return self.count == self.size
    
    def enqueue(self, item):
        if self.is_full():
            raise BufferError("Buffer is full")
        
        self.buffer[self.tail] = item
        self.tail = (self.tail + 1) % self.size
        self.count += 1
    
    def dequeue(self):
        if self.is_empty():
            raise BufferError("Buffer is empty")
        
        item = self.buffer[self.head]
        self.buffer[self.head] = None
        self.head = (self.head + 1) % self.size
        self.count -= 1
        return item
    
    def peek(self):
        if self.is_empty():
            raise BufferError("Buffer is empty")
        return self.buffer[self.head]
```

## Advanced Implementation

### Lock-Free Circular Buffer
```python
from threading import Lock
import ctypes

class AtomicInteger:
    def __init__(self, value=0):
        self._value = ctypes.c_long(value)
    
    def increment(self):
        while True:
            current = self._value.value
            next_value = current + 1
            if ctypes.c_long.from_address(
                ctypes.addressof(self._value)
            ).compare_exchange_strong(current, next_value):
                return next_value
    
    def decrement(self):
        while True:
            current = self._value.value
            next_value = current - 1
            if ctypes.c_long.from_address(
                ctypes.addressof(self._value)
            ).compare_exchange_strong(current, next_value):
                return next_value
    
    @property
    def value(self):
        return self._value.value

class LockFreeCircularBuffer:
    def __init__(self, size):
        self.size = size
        self.buffer = [None] * size
        self.head = AtomicInteger(0)
        self.tail = AtomicInteger(0)
        self.count = AtomicInteger(0)
    
    def enqueue(self, item):
        if self.count.value >= self.size:
            raise BufferError("Buffer is full")
        
        tail = self.tail.value
        self.buffer[tail] = item
        self.tail._value.value = (tail + 1) % self.size
        self.count.increment()
    
    def dequeue(self):
        if self.count.value <= 0:
            raise BufferError("Buffer is empty")
        
        head = self.head.value
        item = self.buffer[head]
        self.buffer[head] = None
        self.head._value.value = (head + 1) % self.size
        self.count.decrement()
        return item
```

### Overwriting Circular Buffer
```python
class OverwritingCircularBuffer:
    def __init__(self, size):
        self.size = size
        self.buffer = [None] * size
        self.head = 0
        self.tail = 0
        self.full = False
    
    def enqueue(self, item):
        if self.full:
            # Overwrite oldest item
            self.head = (self.head + 1) % self.size
        
        self.buffer[self.tail] = item
        self.tail = (self.tail + 1) % self.size
        self.full = self.tail == self.head
    
    def dequeue(self):
        if self.is_empty():
            raise BufferError("Buffer is empty")
        
        item = self.buffer[self.head]
        self.buffer[self.head] = None
        self.head = (self.head + 1) % self.size
        self.full = False
        return item
    
    def is_empty(self):
        return not self.full and self.head == self.tail
```

## Memory-Mapped Circular Buffer
```python
import mmap
import os

class MemoryMappedCircularBuffer:
    def __init__(self, size):
        self.size = size
        # Create memory-mapped file
        self.mm = mmap.mmap(-1, size)
        self.head = 0
        self.tail = 0
        self.count = 0
    
    def enqueue(self, data):
        if len(data) + self.count > self.size:
            raise BufferError("Not enough space")
        
        # Handle wrap-around write
        write_size = min(len(data), self.size - self.tail)
        self.mm.seek(self.tail)
        self.mm.write(data[:write_size])
        
        if write_size < len(data):
            self.mm.seek(0)
            self.mm.write(data[write_size:])
        
        self.tail = (self.tail + len(data)) % self.size
        self.count += len(data)
    
    def dequeue(self, n):
        if n > self.count:
            raise BufferError("Not enough data")
        
        result = bytearray(n)
        read_size = min(n, self.size - self.head)
        
        self.mm.seek(self.head)
        result[:read_size] = self.mm.read(read_size)
        
        if read_size < n:
            self.mm.seek(0)
            result[read_size:] = self.mm.read(n - read_size)
        
        self.head = (self.head + n) % self.size
        self.count -= n
        return bytes(result)
```

## Time Complexity
- Enqueue: O(1)
- Dequeue: O(1)
- Peek: O(1)
- Space: O(n)

## Use Cases

1. Audio/Video Streaming
   - Buffer streaming data
   - Handle producer-consumer scenarios
   - Real-time processing

2. Network Programming
   - Packet buffering
   - Message queues
   - Network protocols

3. Operating Systems
   - Device drivers
   - I/O buffers
   - Interrupt handling

## Advanced Applications

### Streaming Data Handler
```python
class StreamBuffer:
    def __init__(self, size, chunk_size):
        self.buffer = CircularBuffer(size)
        self.chunk_size = chunk_size
    
    def write_stream(self, data_stream):
        while True:
            chunk = data_stream.read(self.chunk_size)
            if not chunk:
                break
            if self.buffer.is_full():
                self.process_buffer()
            self.buffer.enqueue(chunk)
    
    def process_buffer(self):
        while not self.buffer.is_empty():
            chunk = self.buffer.dequeue()
            # Process chunk
            yield chunk
```

### Circular Buffer Pool
```python
class BufferPool:
    def __init__(self, buffer_size, pool_size):
        self.free_buffers = [
            CircularBuffer(buffer_size) 
            for _ in range(pool_size)
        ]
        self.active_buffers = {}
        self.lock = Lock()
    
    def acquire_buffer(self):
        with self.lock:
            if not self.free_buffers:
                return None
            buffer = self.free_buffers.pop()
            self.active_buffers[id(buffer)] = buffer
            return buffer
    
    def release_buffer(self, buffer):
        with self.lock:
            buffer_id = id(buffer)
            if buffer_id in self.active_buffers:
                del self.active_buffers[buffer_id]
                self.free_buffers.append(buffer)
```

## Best Practices

1. Implementation
   - Use power of 2 sizes
   - Handle wraparound correctly
   - Consider thread safety

2. Memory Management
   - Pre-allocate buffer
   - Use appropriate data types
   - Consider memory alignment

3. Performance
   - Minimize copying
   - Use efficient indexing
   - Consider cache effects

## Common Pitfalls

1. Implementation Issues
   - Off-by-one errors
   - Incorrect wraparound
   - Race conditions

2. Memory Issues
   - Buffer overflow
   - Memory leaks
   - False sharing

3. Design Issues
   - Wrong buffer size
   - Inefficient memory usage
   - Poor error handling

## Performance Optimization

### Cache-Friendly Implementation
```python
class CacheOptimizedBuffer:
    def __init__(self, size):
        # Align to cache line
        self.size = size
        self.padding = 64  # Cache line size
        self.buffer = bytearray(size + self.padding)
        self.head = 0
        self.tail = 0
    
    def enqueue(self, item):
        # Ensure aligned access
        aligned_tail = (self.tail + self.padding - 1) & ~(self.padding - 1)
        if aligned_tail + len(item) <= len(self.buffer):
            self.buffer[aligned_tail:aligned_tail + len(item)] = item
            self.tail = aligned_tail + len(item)
```

### Zero-Copy Implementation
```python
class ZeroCopyBuffer:
    def __init__(self, size):
        self.buffer = memoryview(bytearray(size))
        self.head = 0
        self.tail = 0
    
    def enqueue_view(self, data):
        view = memoryview(data)
        n = len(view)
        if self.tail + n <= len(self.buffer):
            self.buffer[self.tail:self.tail + n] = view
            self.tail += n
        else:
            # Handle wraparound
            first_part = len(self.buffer) - self.tail
            self.buffer[self.tail:] = view[:first_part]
            self.buffer[:n - first_part] = view[first_part:]
            self.tail = n - first_part
```
