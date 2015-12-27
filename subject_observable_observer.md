# Subject = Observable + Observer

`subject`是一个神奇的对象，它可以是一个Observable同时也可以是一个Observer：它作为连接这两个世界的一座桥梁。一个主题可以订阅一个Observable，就像一个观察者，并且它可以发射新的数据，或者传递它接受到的数据，就像一个Observable。很明显，作为一个Observable，Observers或者主题都可以订阅它。