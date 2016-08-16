---
layout: post
title: ReactNative Navigator configureScene 自定义动画介绍
date:   2016-08-04 14:49:42 +0800
description: "ReactNative Navigator 自定义过渡动画的实现方式，需要的看过来。妈妈再也不用担心官方提供的动画不好看啦~~~"
author: 尹洁
categories: front article
---

# ReactNative Navigator configureScene 自定义动画介绍

<p style="clear: both;">
<span style="float: right; margin-right: 20px;"> @version <b>rn 0.31</b> </span>
</p>
<p style="clear: both;">
<span style="float: right; margin-right: 20px;"> @supported by <b>曹楠</b> </span>  
</p>
<p><br></p>


ReactNative 已经发布一年多了，其热更新和统一 andriod + IOS 开发的优点也越来越成熟。值得大家去涉猎研究。

## Navigator 过渡动画

Navigator 应该是 RN 的一个核心 component， 相当于 web SPA 的路由。

一般的 APP 在视图“页面”进出栈的时候都会有一个过渡动画，RN 当然也在 Navigator 中有相关的配置。如下：

~~~javascript
    <Navigator
        initialRoute={\{id: 'homepage', params: {}}}
        // initialRouteStack={Route}
        renderScene={this._renderRoute.bind(this)}
        configureScene={(route, routeStack) =>
            SceneConfigs.PushFromRight
            // Navigator.SceneConfigs.PushFromRight             <--- RN 提供的过渡动画
        }
        sceneStyle={{flex: 1, top: 40}}
        navigationBar={
            <Navigator.NavigationBar
                routeMapper={NavigationBarMap}
                style={{backgroundColor: 'gray', marginTop: 0, height: 40, top: 0}}
            />
        }
    />
~~~

其中 `configureScene` 的配置项就用于配置“页面”进出的过渡动画。
官方默认的配置有这几种：

~~~
    Navigator.SceneConfigs.PushFromRight (default)
    Navigator.SceneConfigs.FloatFromRight
    Navigator.SceneConfigs.FloatFromLeft
    Navigator.SceneConfigs.FloatFromBottom
    Navigator.SceneConfigs.FloatFromBottomAndroid
    Navigator.SceneConfigs.FadeAndroid
    Navigator.SceneConfigs.HorizontalSwipeJump
    Navigator.SceneConfigs.HorizontalSwipeJumpFromRight
    Navigator.SceneConfigs.VerticalUpSwipeJump
    Navigator.SceneConfigs.VerticalDownSwipeJump
~~~

还是挺丰富的，不过肯定没法满足大众的种类需求啊 T_T。
所以需要我们自己定义了。

## NavigatorSceneConfigs

先找到官方过渡动画的相关文件。

0.31 版本在这里 `./react-native/Libraries/CustomComponents/Navigator/NavigatorSceneConfigs.js`

有兴趣的可以自己去看一下。

## 自定义动画配置 eg

估计不少人一看到上面文件中一堆代码就没耐心了，实际都是重复的各类动画的配置，关键性的动画实现代码整理如下：

~~~javascript
    'use strict';
    import React, {
        Dimensions,
        PixelRatio
    } from 'react-native';

    import buildStyleInterpolator from 'buildStyleInterpolator';

    const SCREEN_WIDTH = Dimensions.get('window').width,
        SCREEN_HEIGHT = Dimensions.get('window').height;

    const OutToLeft = {
        transformTranslate: {
            from: {x: 0, y: 0, z: 0},
            to: {x: -SCREEN_WIDTH, y: 0, z: 0},
            min: 0,
            max: 1,
            type: 'linear',
            extrapolate: true,
            round: PixelRatio.get()
        },
        translateX: {
            from: 0,
            to: -SCREEN_WIDTH,
            min: 0,
            max: 1,
            type: 'linear',
            extrapolate: true,
            round: PixelRatio.get()
        },
        // transformRotateRadians: {
        //   from: {x: 0, y: 0, z: 0, w: 1},
        //   to: {x: 0, y: 0, z: -0.47, w: 0.87},
        //   min: 0,
        //   max: 1,
        //   type: 'linear',
        //   extrapolate: true
        // },
        // opacity: {
        //     value: 1,
        //     type: 'constant'
        // },
        opacity: {
            from: 1,
            to: 0,
            min: 0,
            max: 0.5,
            type: 'linear',
            extrapolate: false,
            round: 100
        },
        scale: {
            value: 1,
            type: 'constant'
        }
    };

    const InFromRight = {
        transformTranslate: {
            from: {x: SCREEN_WIDTH, y: 0, z: 0},
            to: {x: 0, y: 0, z: 0},
            min: 0,
            max: 1,
            type: 'linear',
            extrapolate: true,
            round: PixelRatio.get()
        },
        translateX: {
            from: SCREEN_WIDTH,
            to: 0,
            min: 0,
            max: 1,
            type: 'linear',
            extrapolate: true,
            round: PixelRatio.get()
        },
        // opacity: {
        //     value: 1,
        //     type: 'constant'
        // },
        opacity: {
            from: 0,
            to: 1,
            min: 0.5,
            max: 1,
            type: 'linear',
            extrapolate: true,
            round: PixelRatio.get()
        },
        scale: {
            value: 1,
            type: 'constant'
        }
    };
    
    const BaseLeftToRightGesture = {

        // If the gesture can end and restart during one continuous touch
        isDetachable: false,

        // How far the swipe must drag to start transitioning
        gestureDetectMovement: 2,

        // Amplitude of release velocity that is considered still
        notMoving: 0.3,

        // Fraction of directional move required.
        directionRatio: 0.66,

        // Velocity to transition with when the gesture release was "not moving"
        snapVelocity: 2,

        // Region that can trigger swipe. iOS default is 30px from the left edge
        edgeHitWidth: 30,

        // Ratio of gesture completion when non-velocity release will cause action
        stillCompletionRatio: 3 / 5,

        fullDistance: SCREEN_WIDTH,

        direction: 'left-to-right'

    };

    const MyScene = {
        // 你自定义的输出的动画名称
        PushFromRight: {
            // A list of all gestures that are enabled on this scene
            gestures: {
                pop: BaseLeftToRightGesture
            },

            // Rebound spring parameters when transitioning FROM this scene
            springFriction: 26,
            springTension: 200,

            // Velocity to start at when transitioning without gesture
            // 默认动画的加速度
            defaultTransitionVelocity: 16,

            // Animation interpolators for horizontal transitioning:
            animationInterpolators: {
                into: buildStyleInterpolator(InFromRight),
                out: buildStyleInterpolator(OutToLeft)
            }
        }
    };

    export default MyScene;

~~~

上述是笔者搞的一个横向无缩放滑动的过渡动画。如果满足你的需要，可以直接拿去用了。下面以此为例简单说明一下。

按接口的输出从后往前简述主要方法。

~~~
    gestures
~~~

手势功能动画，用于定义 APP 中的手势，比如 IOS 中大幅的从左向右滑动会退回上一页面，这个只是简单说明，实际行为的配置在 `BaseLeftToRightGesture` 里，不再多述。大家可以自己看。

~~~
    springFriction
    springTension
~~~

弹簧类动画的摩擦系数、弹性系数，用于微调动画结尾时的“弹性抖动”效果，自己试一下就有所体会。

~~~
    animationInterpolators
~~~

动画关键配置，具体的动画实现就全在这里啦。

以 `OutToLeft` 为例，这是一个返回的过渡动画，效果是页面从左侧平行滑出，无缩放，透明度渐减。

其中有 `translateX`、`opacity`、`scale`、等几个动画属性，看似和 CSS3 的动画很像，这对于前端童鞋来说上手就一下变简单了。

对于不变的属性：  

~~~javascript
    value: 1,                   //属性值，比如长度、透明度、放缩比例、角度等
    type: 'constant'            //类型是常量
~~~

对于变化属性：

~~~javascript
    from: {x: 0, y: 0, z: 0},                   // 初始值
    to: {x: -SCREEN_WIDTH, y: 0, z: 0},         // 终状态值
    min: 0,                                     // 变化范围最小值
    max: 1,                                     // 变化范围最大值
    type: 'linear',                             // 变化速度类型
    extrapolate: true,                          // 预判断
    round: PixelRatio.get()                     // 精确范围，eg.: PixelRatio.get() 精确到屏幕像素比倍数
~~~

目前源码中提到的动画属性有： 

~~~
    transformTranslate          // 3D 位移变化
    translateX                  // X 轴位移变化，类比 Y、Z
    opacity                     // 透明度
    scale                       // 放缩
    transformScale              // 兼容性放缩
    scaleX                      // X 轴放缩比例，类比 Y
    transformRotateRadians      // 旋转弧度
~~~

还算是比较全的，一般的效果都可以满足。具体就看你怎么实现了。

## 结束

完成以上自定义动画，引入后，如第一段代码那样引入即可。

