# serialize
Python has a more primitive serialization module called *marshal*(support Python’s .pyc ):

*pickle* vs *marshal* module:

1. The pickle keeps track of the objects in case of *recursive objects*; These are not handled by marshal
2. pickle(support user-defined classes); marshal not
The marshal serialization format is not guaranteed to be portable across Python versions. Because its primary job in life is to support .pyc files, the Python implementers reserve the right to change the serialization format in non-backwards compatible ways should the need arise. The pickle serialization format is guaranteed to be backwards compatible across Python releases.

# pickle
https://docs.python.org/3/library/pickle.html

    import pickle

## protocol
There are currently 4 different protocols which can be used for pickling.

1. Protocol version 0 is the original ASCII protocol
1. Protocol version 1 is the old binary format
1. Protocol version 2 more efficient pickling of new-style classes.
2. 3 provided by python3.0
2. 4 provided by python3.4: support for very large objects
3. negative version is for select highest version

## Data stream format
pickle Functions:

    dump(object, file[,protocol])
        file: open('a.bin','wb'), StringIO, other.
    dumps(object) -> string
    load(file) -> object
    loads(string) -> object

## 12.1.4. What can be pickled and unpickled?
The following types can be pickled:

    None, True, and False
    integers, floating point numbers, complex numbers
    strings, bytes, bytearrays
    tuples, lists, sets, and dictionaries containing only picklable objects
    functions defined at the top level of a module (using def, not lambda)
    built-in functions defined at the top level of a module
    classes that are defined at the top level of a module
    instances of such classes whose __dict__ or the result of calling __getstate__() is picklable (see section Pickling Class Instances for details).

when class instances/functions are pickled, their class’s code and data are not pickled along with them

    class Foo:
        attr = 'A class attribute'
    $ picklestring = pickle.dumps(Foo)
    b'\x80\x03c__main__\nFoo\nq\x00.'

## Pickling Class Instances¶
When a class instance is unpickled, its `__init__()` method is usually not invoked.
1. creates an uninitialized instance
2. and then restores the saved attributes.

an implementation of this behaviour:

    def save(obj):
       return (obj.__class__, obj.__dict__)

    def load(cls, attributes):
       obj = cls.__new__(cls)
       obj.__dict__.update(attributes)
       return obj

## Handling Stateful Objects
example that shows how to modify pickling behavior for a class.

    class TextReader:
        """Print and number lines in a text file."""

        def __init__(self, filename):
            self.filename = filename
            self.file = open(filename)
            self.lineno = 0

        def readline(self):
            self.lineno += 1
            line = self.file.readline()
            if not line:
                return None
            if line.endswith('\n'):
                line = line[:-1]
            return "%i: %s" % (self.lineno, line)

        def __getstate__(self):
            # Copy the object's state from self.__dict__ which contains
            # all our instance attributes. Always use the dict.copy()
            # method to avoid modifying the original state.
            state = self.__dict__.copy()
            # Remove the unpicklable entries.
            del state['file']
            return state

        def __setstate__(self, state):
            # Restore instance attributes (i.e., filename and lineno).
            self.__dict__.update(state)
            # Restore the previously opened file's state. To do so, we need to
            # reopen it and read from it until the line count is restored.
            file = open(self.filename)
            for _ in range(self.lineno):
                file.readline()
            # Finally, save the file.
            self.file = file

A sample usage might be something like this:

    >>>
    >>> reader = TextReader("hello.txt")
    >>> reader.readline()
    '1: Hello world!'
    >>> reader.readline()
    '2: I am line number two.'
    >>> new_reader = pickle.loads(pickle.dumps(reader))
    >>> new_reader.readline()
    '3: Goodbye!'
