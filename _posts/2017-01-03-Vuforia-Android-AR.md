
---
layout: post
title: 使用Vuforia SDK 构建Android AR 程序
categories: [AR]
description: 
keywords: AR,Vuforia
---

## 关于AR

AR(Augmented Reality)终于在2016年因为Pokemon GO大火了一把。

<img src="/images/posts/AR/1.png" width="80%"/>

目前常见的AR技术是CV(Computer Vison)和CG(Computer Graphic)的结合。CV就是用各种成象系统代替视觉器官作为一种输入的手段，让机器来完成对视觉得到的结果完成处理和解释，让计算机能够感知环境。CV的实现方案有很多，比如FREAK(一种自然图片的识别算法)+ICP(一种自然图片的跟踪算法)、SLAM(一种环境构建算法)等。CG就是通常我们理解那种意思，在AR中，就是利用CV算法获取到图形相关的信息进行绘制。而Pokemon GO的CV其实就是LBS。

AR在技术层面上就是和人一样识别一个真实的环境，在机器的视角里建立一个一模一样的虚拟世界，把真实环境和虚拟世界融合。这样就可以把虚拟信息显示在真实的环境里。而绘制的过程最终要的是建立一个相机坐标系，这个坐标系依据是判断现实场景的物体的信息。构建相机坐标系有两种方式：一是识别已经注册的标记（比如图片、二维码、自然无物体）；二是基于LBS，利用GPS建立世界坐标体系，然后确定虚拟信息和相机在世界坐标体系中的位置，这样就可以确定真实物体和虚拟消息在相机坐标中的位置，就可以融合在一起。

目前在应用层面上，可用的SKD很多，好坏说法不一，比较感兴趣的有下面这几个：

* Vuforia
商业SDK，原属高通，目前已经卖掉，使用广泛，资料相对全面 。[\[官网地址\]](https://developer.vuforia.com/)
*  ARToolKit
开源SDK，历史悠久，识别效果一般，但是由于开源，对于对AR底层感兴趣的人来说，是个不错的开始。[\[官网地址\]](http://artoolkit.org/)
* EasyAR
免费不开源，国产，口碑不错。[\[官网地址\]](http://www.easyar.cn/)

## Vuforia简介

Vuforia是一个用于创建AR应用程序的SDK。开发者可以轻松地向自己的应用程序中添加AR的功能，使得我们的程序可以识别图像或者是在现实世界中重建环境。

##使用Unity3D和Vuforia完成AR Android App

 Vuforia可以配合Uniyt3D来完成一个AR的App，使用的工具有：
 
*  Vuforia 6.2 
*  Unity 3D 5.4
*  Android环境


### Voforia的使用准备

首先在[Voforia官网](https://developer.vuforia.com/)注册帐号，进入『Develop』，为了使用Voforia需要做下面两件事：

1. 为自己的Android App创建License。
license Key 用来作为Vuforia 对此应用的唯一标识，位于Develop>>License Manager。

<img src="/images/posts/AR/2.png" width="80%"/>

填写应用名称，跟随网站向导就也可以生产License Key。

<img src="/images/posts/AR/3.png" width="80%"/>

在License Manager中可以查看到Key值，稍后在Unity中使用。

<img src="/images/posts/AR/4.png" width="80%"/>

2. 准备数据和跟踪标记
将图片作为跟踪标记，在网站制作为数据集。路径为：Develop>>Target Manager ，添加数据集。

<img src="/images/posts/AR/5.png" width="80%"/>

一个数据集可以包含多个跟踪标识，跟踪标记可以是图片甚至是3D文件，这里以图片为例，Width属性官方建议50.

<img src="/images/posts/AR/6.png" width="80%"/>

上传图片之后查看Rating值，此值代表上传图片作为跟踪目标的质量。

<img src="/images/posts/AR/7.png" width="80%"/>

检查跟踪目标质量也可以选择数据集的Show features，查看用来识别的图像特征。对于图片来说Vuforia识别的是图片特征比如灰度、对比度之类的，与图片的颜色无关。
然后下载此数据集，稍后供Unity使用。

### Unity集成Vuforia

Unity是一个的综合型游戏开发工具，是一个全面整合的专业游戏引擎。Vuforia专门针对Unity出了插件，方便开发者使用。

在Vuforia官网下载插件包，导入到在新建的Unity项目中去，Assets>>Import Package>>Custom Package,这里需要导入两个东西，一个是从官网制作的跟踪目标的数据集，另一个是Vuforia 5 SDK for Unity 插件。 

<img src="/images/posts/AR/8.png" width="80%"/>

#### 导入模型

Unity可以使用多种格式类型的3D模型，这里支持的模型类型其实很多，创建格式的3D模型都已经支持，可以去各种资源网站寻找，这里尝试使用的是.fbx格式的模型，是一种包含动画内容的3D模型格式，常见的模型制作软件3DMax和Maya都可以导出这种格式。

#### 在Unity中使用Vuforia

在Unity的当前scene中添加Vuforia的ARCamera，这个类抽象的是使用设备观察AR模型的视角，使用这个视角替换Unity中的常规视角，设置视角的位置位于原点，位置可以在ARCamera属性中设置。同时删除Unity自己的Camera类。

<img src="/images/posts/AR/9.png" width="80%"/>

为了使ARCamera能使用，将之前的Vuforia的License key添加到ARCamera的属性中。此时在Game标签下应该可以查看到Vuforia的视角。

然后应当添加标记跟踪标记到目前的scene中，之前导入的路径为Assets>>Vuforia>>Prefabs,然后设置跟踪标记的的位置，此位置要根据模型大小，下调图片标记的位置。

<img src="/images/posts/AR/10.png" width="80%"/>

再将模型导入到跟踪标记中（此处为ImageTarget），作为跟踪标记的子节点，这样当设备扫描到跟踪目标时就可以显示其所有自己的的模型，再调整模型的大小的参数。

<img src="/images/posts/AR/11.png" width="80%"/>

然后绑定模型和跟踪标记，在ARCamera中选择Load Data Set Image_Target和Activite选项。

<img src="/images/posts/AR/12.png" width="80%"/>

完成后壳通过Unity进行测试，点击RUN。可以通过电脑摄像头识别标识显示模型。

#### 使用模型中的动画

Unity控制动画有多种方法，这里尝试使用的是4.0版之后新加入的Mecanim动画机制。

首先，由于3D模型的动画制作方式不同，要在导入的模型属性中选择正确的类型，否则在Unity3D中无法识别。

<img src="/images/posts/AR/13.png" width="80%"/>

当Unity可以识别3D模型的动画类型后可以在Assets中预览动画。!

<img src="/images/posts/AR/14.png" width="80%"/>

在Assets中看到的动画以动画素材为单元。动画素材（Animation Clips）指的是每个不重复的动画单元，比如"行走"这个动作，在模型制作时，只制作完整的一步行走，这个动作的开始和结尾是相同的，如果要完成多步行走的动画，只需要多次调用"行走"这个Animation Clips就可以了。

如何调用Clips来组成我们想要的动画，这里使用Mecanim动画机制。Mecanim把游戏中的角色设计提高到了一个新的层次，使用Mecanim可以通过Retargeting来提高角色动画的重用性。在处理人类角色动画时，开发者可以使用动画状态机来处理动画之间的过渡及动画之间的逻辑。

一个3D模型在Mecanim机制中想要展示动画必须要有Animator组件，如下图所示：

<img src="/images/posts/AR/15.png" width="80%"/>

包含两个主要的内容，一个是Avatar，当模型是人形模型时，需要定义这个部分，但这部分在模型的制作时就已经添加了，所以开发者不用关心，第二个内容是Animator Controller，它负责定义使用哪些动画素材，以及何时、如何播放这些动画素材，需要开发者来添加。

由于Animator Controller是用来控制播放哪些动画素材的，所以这里需要创建一个新的：

<img src="/images/posts/AR/16.png" width="80%"/>

Animator Controller以状态机（State Machine）的方式维护一系列动画，且当隶属的角色发生特定事件时，切换播放的动画。

把Animation Clip从Project窗口中拖到Animator窗口，则自动创建了一个对应的状态节点，第一个拖入的Animation Clip为默认状态节点，然后拖入不同的Animation Clip，表示从一个动画状态变换到另一个状态，比如从站立到行走。

用两个Animation Clip之间的连线表示转换条件，当条件满足时切换状态。可以选择两个状态之间的连线，设置一个转换条件。

<img src="/images/posts/AR/17.png" width="80%"/>

条件可以用四种类型的值设置，当值发生变化，动画就会切换。还有一些更细粒度地控制动画切换属性的设置也可以使用。

<img src="/images/posts/AR/18.png" width="80%"/>

到这里动画已经可以根据一些值的改变而切换了，现在应该处理的是如何改变这些状态值来达到切换动画的目的。

#### 用脚本控制动画

Unity支持两种编程语言来编写动画脚本，分别是JavaScript和C#，此处尝试用C#来编写。创建C#脚本。

<img src="/images/posts/AR/19.png" width="80%"/>

将脚本添加到模型中：

<img src="/images/posts/AR/20.png" width="80%"/>

可以使用Unity自带的编辑器MonoDevelop来编写，Unity中的脚本语法可以参考[手册](http://wiki.ceeger.com/ceeger.php)。

<img src="/images/posts/AR/21.png" width="80%"/>

这里要与界面交互有两种方式，一种是先完成Unity部分的工作导出Android工程，在使用Android构建界面与脚本交互，一种是直接使用Unity中的界面和脚本交互，此处尝试了后者。最后的界面如下图所示。Unity中界面编程语法参照[此处](http://blog.csdn.net/u013289188/article/details/29618897)。

<img src="/images/posts/AR/22.png" width="80%"/>

### 导出为Android工程
这里使用的是Unity导出为Android工程的方法，由于使用了Voforia的SDK，所以AR的部分也将随之一起导出。

首先存储当前的Scene，然后打开File>>Build Settings，将保存的Scene选中，然后选择导出的平台 Android。

<img src="/images/posts/AR/23.png" width="80%"/>

在Player Setting中设置导出的包名、目标版本等相关信息，再选中Google Android Project选项，点击Build And Run就可以导出为Android项目，再使用Android Studio进行开发。

### Demo效果

这里完成了一个特警动画Demo，可以通过界面交互改变特警的动作。

<img src="/images/posts/AR/24.png" width="80%"/>

<img src="/images/posts/AR/25.png" width="80%"/>

