---
title: 工作日志
slug: xq9aqxe3h1rriyp6
url: https://www.yuque.com/caogongzi/pu6995/xq9aqxe3h1rriyp6
---

# 十月二十七日
按G键显示太阳和月亮调节

cloud coverage

sun light intensity

fog density

manually position sun target

time of day

manually position moon target

animate time of day 

day of length

night length



![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1761554310263-ca56765e-3bae-47ab-b253-20b93116b0f4.png)

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1761554476594-5d11af56-7779-4d5a-a99c-9336f3e294ff.png)

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1761554498921-b954c2ad-3f73-43a4-9b11-682ea2b40058.png)

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1761555854909-90b1b3d5-6897-42c1-8963-3b280f859911.png)

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1761555889096-e8d0f5a4-a87f-4334-a46d-6ca70f26c431.png)

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1761555933502-0c48b7c8-d485-4d8e-a5a1-e236e04167b8.png)





# 十月二十五日


# 十月二十四日
下午

2h 输进入文档

1h 做最后修改

# 十月二十二日
- [x] 熟悉导入solidworks组件到UE5的方法。
- [x] 整理海况环境对船只影响（直接关乎仿真颗粒度），从自己脑子里输出



# 十月二十一日
存在问题：

- [x] 曝光减少
- [x] 让相机跟随船只移动



# 十月二十日
1. 发现一开始的漂移是正常现象。漂移之后的位置确实是正确位置
2. 发现船体其实是有动的，只是60以下的移动速度过慢，100以上才会有比较明显的移动效果
3. sim3dLevelActor是继承通用关卡父类LevelActor的，所以拥有LevelActor的所有属性。亲测，将主工程文件的关卡蓝图的父类更换为sim3dLevelActor以后，正常运行不会报错
4. 完成摄像头在主工程文件获取画面
5. 在UE5中放置摄像头视角。在simulink中改用3d camera get
6. 解决了曝光问题，后续再微调一下



存在问题：

- [x] 现在调试视角非常麻烦，需要在UE5中放置摄像头视角。
- [x] 相机曝光参数存在问题。太暗了。而且video display边缘有很大的光。这些都不太正常。

# 十月十七日




camera跟随被控对象移动的方法：

方案1：直接对camera提供输入，输入是经过换算以后的camera直接相对原点位姿。 

方案2：camera和simulation 3d actor链接，simulation 3d actor传输进被控对象的位姿即可。

拟采用方案2，更加优雅。



- [x] 但是问题是，我的主工程项目里，ue5的关卡蓝图的父类不能改成sim 3d level script actor，否则会报错。但是如果不改成这个父类，又不能用camera。

下面首要解决这个问题。

# 十月十六日
上午：和李教授沟通。 啥也没做。

下午：就做了一个表格加一张图






