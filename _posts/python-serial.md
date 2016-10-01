# serialize
Python has a more primitive serialization module called *marshal*(support Python’s .pyc ):

*pickle* vs *marshal* module:

1. The pickle keeps track of the objects in case of *recursive objects*; These are not handled by marshal
2. pickle(support user-defined classes); marshal not
The marshal serialization format is not guaranteed to be portable across Python versions. Because its primary job in life is to support .pyc files, the Python implementers reserve the right to change the serialization format in non-backwards compatible ways should the need arise. The pickle serialization format is guaranteed to be backwards compatible across Python releases.

# pickle
https://docs.python.org/3/library/pickle.html

    import pickle

Functions:

    dump(object, file[,protocol])
        file: fileHandler, StringIO, other.
    dumps(object) -> string
    load(file) -> object
    loads(string) -> object

## protocol
There are currently 4 different protocols which can be used for pickling.

1. Protocol version 0 is the original ASCII protocol
1. Protocol version 1 is the old binary format
1. Protocol version 2 more efficient pickling of new-style classes.
2. 3 provided by python3.0
2. 4 provided by python3.4: support for very large objects
3. negative version is for select highest version

## Data stream format¶
