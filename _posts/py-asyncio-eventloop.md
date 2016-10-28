
# Delayed calls
> asyncio.sleep is based on eventloop.call_later
The event loop has its own *internal clock* for computing timeouts. Which will generally be a different clock than `time.time()`.

    AbstractEventLoop.call_later(delay, callback, *args)
        Arrange for the callback to be called after the given delay seconds (either an int or float).
        An instance of asyncio.Handle is returned(can be used to cancel the callback)
        callback will be called *exactly once*(not setinterval) per call to call_later().

    AbstractEventLoop.call_at(when, callback, *args)
        Arrange for the callback to be called at the given absolute timestamp
        Use functools.partial to pass keywords to the callback.
    AbstractEventLoop.time()
        Return the current time, as a float value, according to the event loop’s internal clock.
