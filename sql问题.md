# sql问题

比如，有个需求，我要统计不同的报警信息，这个可以先分组根据报警设备，然后统计个数，最后封装对象。这是直接写sql。

但是我也可以先把表查一遍，拿到所有的数据对象，在遍历对象的list，如果是相同的对象就计数，最后得到想要的结果，这样sql会非常简单，直接select* 就可以了。但是业务上会有过滤的代码。

不过前一种的方式sql可能会比较难写，但是业务上就直接得到结果了，直接返回就可以了。

这两种方式哪一种会好一点呢？性能上？