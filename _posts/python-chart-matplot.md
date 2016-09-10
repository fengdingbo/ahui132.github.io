# matplotlib

## pylab
pylab 是matplotlib 面向对象绘图库的一个接口, 与matlab 相近

    # 不带参数, gui 退出会有问题
    ipython
    # or
    ipython --matplotlib
    > from pylib import *


## 来个简单的
> 原文: http://www.labri.fr/perso/nrougier/teaching/matplotlib/
> 译文: http://liam0205.me/2014/09/11/matplotlib-tutorial-zh-cn/

### plot sin

    $ ipython --matplotlib
    $ cat a.py
    from pylab import *

    X = np.linspace(-np.pi, np.pi, 256,endpoint=True)
    C,S = np.cos(X), np.sin(X)

    plot(X,C)
    plot(X,S)
    # python will not exit
    show()

### Instantiating defaults, 默认设置

```
# 导入 matplotlib 的所有内容（nympy 可以用 np 这个名字来使用）
from pylab import *

# 创建并激活一个 8 * 6 点（point）的figure图，并设置分辨率为 80
figure(figsize=(8,6), dpi=80)

# 创建一个新的 1 * 1 的子图，接下来的图样绘制在其中的第 1 块（也是唯一的一块）
subplot(1,1,1)
# subplog(2,3,2); # 选择2*3=6块中的第二块

X = np.linspace(-np.pi, np.pi, 256,endpoint=True)
C,S = np.cos(X), np.sin(X)

# 绘制余弦曲线，使用蓝色的、连续的、宽度为 1 （像素）的线条
plot(X, C, color="blue", linewidth=1.0, linestyle="-")

# 绘制正弦曲线，使用绿色的、连续的、宽度为 1 （像素）的线条
plot(X, S, color="green", linewidth=1.0, linestyle="-")

# 设置横轴的上下限
xlim(-4.0,4.0)

# 设置横轴记号
xticks(np.linspace(-4,4,9,endpoint=True))

# 设置纵轴的上下限
ylim(-1.0,1.0)

# 设置纵轴记号
yticks(np.linspace(-1,1,5,endpoint=True))

# 以分辨率 72 来保存图片
# savefig("exercice_2.png",dpi=72)

# 在屏幕上显示
show()
```

### 修改颜色 样式
```
plot(X, C, color="blue", linewidth=2.5, linestyle="-")
```

### plot limit 图片边界

    xlim(X.min()*1.1, X.max()*1.1)

### plot tick,记号

    xticks( [-np.pi, -np.pi/2, 0, np.pi/2, np.pi])
    yticks([-1, 0,0.5, +1])

![python-chart-matplot-1.png](/img/python-chart-matplot-1.png)

### plot tick lables

    plt.xticks([-np.pi, -np.pi/2, 0, np.pi/2, np.pi],
           [r'$-\pi$', r'$-\pi/2$', r'$0$', r'$+\pi/2$', r'$+\pi$'])

    plt.yticks([-1, 0, +1],
           [r'$-1$', r'$0$', r'$+1$'])

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
