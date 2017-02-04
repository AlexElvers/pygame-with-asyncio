# pygame with asyncio
This is a simple example how to connect pygame with asyncio.

The example uses a queue for handling pygame events. It runs a loop (`pygame_event_loop`) that is requesting pygame events with blocking (`pygame.event.wait()`). Using `pygame.event.get()` would produce high CPU load because it is not blocking. This event loop is started with `run_in_executor` that uses a thread pool (you could set the size of the thread pool to 1). A direct call from the main thread would not work because asyncio expects that functions use `await` for blocking operations (or `yield from` in older Python versions). The asyncio queue is not thread safe, so you have to use `run_coroutine_threadsafe` for putting events on the queue.

Handling events now works by using `await event_queue.get()` in a coroutine.

```python
def pygame_event_loop(loop, event_queue):
    while True:
        event = pygame.event.wait()
        asyncio.run_coroutine_threadsafe(event_queue.put(event), loop=loop)
```

The image of the ball is copied from the pygame documentation.
