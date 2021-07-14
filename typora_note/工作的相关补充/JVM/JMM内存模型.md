

![image-20210310105352046](JMM内存模型.assets/image-20210310105352046.png)

![image-20210310105413145](JMM内存模型.assets/image-20210310105413145.png)

![image-20210310105747938](JMM内存模型.assets/image-20210310105747938.png)



<img src="JMM内存模型.assets/image-20210310133928426.png" alt="image-20210310133928426" style="zoom:50%;" />





<img src="JMM内存模型.assets/image-20210310133947752.png" alt="image-20210310133947752" style="zoom:50%;" />

本地内存 存着主内存的副本

不同线程 不能访问对方的 变量。 线程之间的通信必须通过主内存完成。

JMM的可见性（通知机制）



![image-20210310134703415](JMM内存模型.assets/image-20210310134703415.png)



![image-20210310134842914](JMM内存模型.assets/image-20210310134842914.png)

![image-20210310134927573](JMM内存模型.assets/image-20210310134927573.png)

<img src="JMM内存模型.assets/image-20210310135017928.png" alt="image-20210310135017928" style="zoom: 50%;" /><img src="JMM内存模型.assets/image-20210310135105171.png" alt="image-20210310135105171" style="zoom:50%;" />

有个人应该告诉 main  我这边

![image-20210310135307712](JMM内存模型.assets/image-20210310135307712.png)



A线程修改了，写回了主内存，但是main线程不知道









所有加载  现有模版 静态先行 静态值加载一次

静态 >构造块>构造方法









