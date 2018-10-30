---
title: react-navigation
date: 2018-05-17
categories: react-native
---

主要使用的是TabNavigation和StackNavigation。

# StackNavigation

`StackNavigation`用于配置屏幕切换，相当于路由。
**参数：**
RouteConfigs： 路由的配置，相当于一个路由列表
StackNavigatorConfig： 其他配置，可设置初始路由等。
具体API可参考 [react-navigation官网](https://v1.reactnavigation.org/docs/api-reference.html)

使用：
常用的配置在代码中已经进行了注释。其中的TabBar组件是使用TabNavigation生成的底部tab栏。下面会继续介绍。

```js
import React, { Component } from 'react';
import { StackNavigator } from 'react-navigation';
import CardStackStyleInterpolator from 'react-navigation/src/views/CardStack/CardStackStyleInterpolator';
import TabBar from './TabBar';
//....其余组件导入
// 配置整个项目的导航
const RootStack = StackNavigator(
    {
        Login: {
            screen: Login,
            navigationOptions: {
                header: null//去掉顶部标题栏
            }
        },
        Main: {
            screen: TabBar,
        },
        BleDevices: {
            screen: BleDevices,
        },
        SystemSet: {
            screen: SystemSet,
        },
        PersonalData: {
            screen: PersonalData,
        },
    },
    {
        initialRouteName: 'Login',//项目初始路由
        transitionConfig: () => ({//修改整个栈内的页面跳转动画：从右向左
            screenInterpolator: CardStackStyleInterpolator.forHorizontal
        }),
        navigationOptions: {
            // headerLeft: null,//去掉返回的按钮
            headerRight: <View/>,
            headerBackTitle: null,//设置跳转页面左侧返回箭头后面的文字，默认是上一页的标题
            headerStyle: {//设置顶部标题栏的样式
                backgroundColor: '#3ce6e6',
                height: 50,
            },
            headerTitleStyle: {//顶部标题栏文字样式
                color: 'white',
                fontSize: 18,
                flex: 1,
                textAlign: 'center',
                alignSelf:'center',
            },
            gesturesEnabled: true,//是否可以使用手势来消除此屏幕,在iOS上默认为true，在Android上为false。
            gestureResponseDistance: {//设置手势识别距离屏幕边缘的的位置
                horizontal: 300
            }
        }
    }
);

export default RootStack;
```

**遇到的问题**

- 在安卓上，顶部header里的标题不能在整行里居中显示（偏右，其实是在返回箭头右侧区域内居中的）。
> 经过调试发现是由于左侧有返回箭头导致的。为了使标题居中，因此加上了`headerRight: <View/>`。

# TabNavigation

上节中提到的`TabBar`，即是TabNavigation。用于制作顶部或者底部tab栏。

TabBar.js

```js
import React, { Component } from 'react';
import { Image } from 'react-native';
import { TabNavigator } from 'react-navigation';
import Monitor from '../components/Monitor';
import History from '../components/Hisroty';
import Mine from '../components/Mine';

//配置底部菜单栏
const TabBar = TabNavigator(
    {
        Monitor: {
            screen: Monitor,
            navigationOptions: {
                headerTitle: '监测',
                tabBarLabel: '监测',
                tabBarIcon: ({tintColor}) =>(
                    <Image
                        source={require('../assets/images/ic_monitor.png')}
                        style={[{width: 20, height: 20},{tintColor: tintColor}]}
                    />
                )
            },
        },
        History: {
            screen: History,
            navigationOptions: {
                headerTitle: '历史记录',
                tabBarLabel: '历史记录',
                tabBarIcon: ({tintColor}) => (
                    <Image
                        source={require('../assets/images/ic_history.png')}
                        style={[{width: 20, height: 20},{tintColor: tintColor}]}
                    />
                )
            },
        },
        Mine: {
            screen: Mine,
            navigationOptions: {
                headerTitle: '我的',
                tabBarLabel: '我的',
                tabBarIcon: ({tintColor}) => (
                    <Image
                        source={require('../assets/images/ic_mine.png')}
                        style={[{width: 20, height: 20},{tintColor: tintColor}]}
                    />
                )
            },
        },
    },
    {
        tabBarPosition:"bottom",//设置tab栏所在的位置
        swipeEnabled: true,//在切换tab时是否显示动画
        animationEnabled: true,//是否允许在标签之间滑动
        backBehavior: 'none',//按back键是否跳转到第一个tab（initialRoute），none为不跳转，默认是initialRoute
        tabBarOptions: {
            showIcon: true,//是否显示图标，默认关闭
            showLabel: true,//是否显示label，默认开启
            activeTintColor: '#EB3695',//设置活动tab的颜色
            inactiveTintColor: 'gray',//设置不活动tab的颜色
            style: {//设置tabNavigator的样式
                backgroundColor: '#f8f8f8',
                height: 55
            },
            indicatorStyle: {//标签指示器的样式对象（选项卡底部的行）。安卓底部会多出一条线，可以将height设置为0来暂时解决这个问题
                height: 0
            },
            labelStyle: {//label的样式
                fontSize: 13,
                marginTop: -5,
                marginBottom: 5,
            },
            iconStyle: {
                marginBottom: 5
            }
        }
    }
);

export default TabBar;
```

很神奇的事情（请恕我无知）：
- tab栏的图标使用的是图片，设置tintColor竟然可以改变图片的颜色，AMAZING ~ ~ 

**注意**：
当TabNavigator和StackNavigator组合使用时，使用this.props.navigation依然可以跳转TabNavigator中定义的组件。

```js
const RootStack = StackNavigator({
  Page1: {
    screen: Page1,
  },
  Page2: {
    screen: TabBar
  }
})
const TabBar = TabNavigator({
  Tab1: {
    screen: Tab1,
  },
  Tab2: {
    screen: Tab2
  }
})

// 页面中的使用：
this.props.navigation.navigate("Tab2");
```

一开始因为跳转指定tab的问题纠结了好大一会，结果发现是自己又无知了一次。。。感谢RN让我发现自己是多么的孤陋寡闻......