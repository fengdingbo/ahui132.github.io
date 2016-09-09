# matplotlib

## plot
plot( [ dt(2013, 1, 1), dt(2013, 5, 17)], [ 1 , 1 ], linestyle='None', marker='.')


## with date()

    import datetime as dt
    dates = ['01/02/1991','01/03/1991','01/04/1991']
    x = [dt.datetime.strptime(d,'%m/%d/%Y').date() for d in dates]
    y = range(len(x)) # many thanks to Kyss Tao for setting me straight here

    import matplotlib.pyplot as plt
    import matplotlib.dates as mdates

    plt.gca().xaxis.set_major_formatter(mdates.DateFormatter('%m/%d/%Y'))
    plt.gca().xaxis.set_major_locator(mdates.DayLocator())
    plt.plot(x,y)
    plt.gcf().autofmt_xdate()

## line

    import numpy as np
    import matplotlib.pyplot as plt

    x = np.linspace(0, 2 * np.pi, 100)
    y1 = np.sin(x)
    y2 = np.sin(3 * x)
    plt.fill(x, y1, 'b', x, y2, 'r', alpha=0.3)
    plt.show()

## save
show() 后会清数据..

    plt.savefig('foo.png')

whitespace around the image. Remove it with:

    savefig('foo.png', bbox_inches='tight')

# label
matplotlib: how to decrease density of tick labels in subplots?
