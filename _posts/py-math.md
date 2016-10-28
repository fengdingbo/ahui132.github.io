---
layout: page
title:
category: blog
description:
---
# Preface


# random, range

## random

	import random

	random.randint(1, 20)
		20
	random.random() 0~1
		0.1234

## range

### range int

	list(range([start=0,] end, [step]))

### range float
得到 *numpy.ndarray* :

    from pylab import *
    list(np.linspace(start, end, amount, endpoint=False))
    list(np.linspace(1, 3, 3,))
        [1.0, 2.0, 3.0]
    list(np.linspace(1, 3, 3, endpoint=False))
        [1.0, 1.66, 2.33]

# min,max

    array([3,9,2]).min()

# geometry

## sin
np.sin support: number, array, numpy.ndarray

    np.sin(np.pi)
    np.sin([1,2])

    X = np.linspace(-np.pi, np.pi, 256,endpoint=True)
    C,S = np.cos(X), np.sin(X)
