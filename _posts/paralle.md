# 并行 并发区别：
> http://stackoverflow.com/questions/1050222/concurrency-vs-parallelism-what-is-the-difference）

并行是指程序的运行状态，要有两个线程正在执行才能算是Parallelism；
并发指程序的逻辑结构，Concurrency则只要有两个以上线程还在执行过程中即可。

简单地说，Parallelism要在多核或者多处理器情况下才能做到，而Concurrency则不需要。
