---
title: react-plugins总结
date: 2018-08-10
categories: react-native 
---

# react-native-pdf 预览PDF

* 配置：
```bash
yarn add react-native-pdf
yarn add react-native-fetch-blob
react-native link react-native-pdf
react-native link react-native-fetch-blob
```

* 使用: 此栗子为动态展示pdf
```jsx
import Pdf from 'react-native-pdf';

export default class ViewPDF extends Component {

    constructor() {
        super();
        this.state={
        //初始化source务必带着uri,否则会报错。这样的写只会报一个警告
            source: {uri: ''},
        }
    }

    componentWillMount() {
        fetch(BaseURL+'/previewReport?id='+id)
            .then((res) => res.json())
            .then((json) =>{
                const url = json.url;//url为pdf的地址
                this.setState({
                    source: {
                    	uri: url, 
                    	cache:false//不进行pdf的缓存
                    },
                })
            })
            .catch((error) =>{
                console.log(error)
            })
    }

    render() {
       // const source =  {uri: 'http://192.168.1.221:8080/healthme/report/49.pdf', cache:false};
        return (
            <View style={{flex: 1,width: '100%',justifyContent: 'flex-start', alignItems: 'center', backgroundColor: '#fff'}}>
                <Pdf
                    source={this.state.source}
                    onLoadComplete={(numberOfPages,filePath)=>{
                        console.log(`number of pages: ${numberOfPages}`);
                    }}
                    onPageChanged={(page,numberOfPages)=>{
                        console.log(`current page: ${page}`);
                    }}
                    onError={(error)=>{
                        console.log(error);
                    }}
                    style=			{{flex:1,width:Dimensions.get('window').width,}}/>
            </View>
        )
    }
}
```

# react-native-linear-gradient 渐变背景

* 配置：
```bash
yarn add react-native-linear-gradient
react-natvie link react-native-linear-gradient
```

* 使用
  **属性：**color--渐变色的范围，为一个颜色数组
```jsx
import LinearGradient from 'react-native-linear-gradient';

class Login extends Component {
	render() {
      return (
      	<LinearGradient 
      		colors={['#25a771', '#21979d']} 
      		style={{flex: 1}}
      	>
      		<View>......</View>
		</LinearGradient>
      )
	}

}
```

上面的用法绘制的渐变为从上到下的渐变。若想绘制横向的渐变，需要加上**start**和**end**属性

```jsx
<LinearGradient start={{x: 0, y: 0}} end={{x: 1, y: 0}} colors={['#25a771', '#21979d']} style={styles.linearGradient}>
  <Text style={styles.buttonText}>
    Sign in with Facebook
  </Text>
</LinearGradient>
```

上面的用法全为均匀的渐变，so，
**属性：**locations--范围数组，表示某个颜色占据的范围

```jsx
<LinearGradient 
	start={{x: 0, y: 0}} 
	end={{x: 1, y: 0}} 
	locations=[0.3,1]//表示第一个颜色占据0-30%，第二个颜色占据30%-100%
	colors={['#25a771', '#21979d']} 
>
  <Text style={styles.buttonText}>
    Sign in with Facebook
  </Text>
</LinearGradient>
```

可任意调整start，end，locations，绘制自己需要的渐变色。

# react-native-slider 滑动进度条

其实react-native本身自带Slider组件，但是由于样式不能自由控制，因此使用了插件。

* 配置：
```bash
yarn add react-native-slider
```

* 使用
```jsx
import Slider from 'react-native-slider';

class SliderExample extends React.Component {
  state = {
    sliderValue: 0.2
  };

  render() {
    return (
      <View style={{flex: 1}}>
        <Slider
          value={this.state.sliderValue}//设置滑块所在的位置
          thumbStyle={{borderColor: '#19A770', borderWidth: 7, width: 26,height: 26,borderRadius: 13}} //设置滑块的样式，此处是设置了一个粗的border
          trackStyle={{height: 14}}//设置滑块轨道的样式
          maximumValue={100}//进度条最大值，默认是1
          onValueChange={sliderValue => this.setState({sliderValue})}
          onSlidingComplete={this.getNextRecord}//滑动结束事件
          thumbTintColor={'#fff'}//滑块的背景颜色
          maximumTrackTintColor={'#adadad'}//滑块右侧轨道颜色
          minimumTrackTintColor={'#545454'}//滑块左侧轨道颜色
        />
      </View>
    );
  }
}
```

详细配置可查看[react-native-slider文档](https://github.com/jeanregisser/react-native-slider)

# react-native-tab-view

* 配置：
```bash
yarn add react-native-tab-view
```
* 需求：tab的内容为一个列表，列表的每一项都有一个按钮，点击按钮跳转页面展示相应项的数据。

* 使用：
```jsx
import { TabView, TabBar, SceneMap } from 'react-native-tab-view';
import UnsubmitItem from '../items/UnsubmitItem';
import ReportedItem from '../items/ReportedItem';
import DiagnosisItem from '../items/DiagnosisItem';
export default class History extends Component {
    constructor() {
        super();
        this.state = {
            index: 0,
            routes: [
                { key: 'unsubmit', title: '未提交诊断' },
                { key: 'diagnosing', title: '诊断中' },
                { key: 'reported', title: '已出报告' },
            ],//可按自己想要的顺序排列
        };
    }

//设置每个tab的样式
    _renderTabBar = props =>{
        return (<TabBar
            {...props}
            style={styles.tabBar}
            pressColor={'#19A770'}
            labelStyle={{color: '#19A770'}}
            indicatorStyle={{ backgroundColor: '#19A770'}}
        />)
    }

    viewDetails = (id) => {
    // id为组件中传过来的数据
        this.props.navigation.navigate('DataDetails',{id: id})
    }

    render() {
        return (
            <TabView
                style={{backgroundColor: '#f8f8f8'}}
               //页面上tab的顺序与routes数组中的顺序有关，即按数组中的索引顺序排列
                navigationState={this.state}
                renderScene={SceneMap({
                //属性皆为回调函数
                    first: () => (
                        <UnsubmitItem viewDetails={this.viewDetails}/>
                    ),
                    second: () => (
                        <DiagnosisItem/>
                    ),
                    third: () => (
                        <ReportedItem viewDetails={this.viewDetails}/>
                    ),
                })}
                renderTabBar={this._renderTabBar}
                onIndexChange={index => this.setState({ index })}
            />
        )
    }
}
```

**问题**
以上的用法中，每次点击一个tab，其余三个tab都会重新mount，多余的加载了很多次。
**原因**
每次点击tab时，index都会改变；index一改变，就会调用renderScene方法。因此每次三个tab组件都会重新加载。
**解决办法**
* 第一步：
  修改renderScene的方法：`renderScene={this._renderScene}`
```js
_renderScene = ({route}) => {
        switch (route.key) {
            case 'diagnosing':
                return <DiagnosisItem index={this.state.index}/>;
                break;
            case 'unsubmit':
                return <UnsubmitItem viewDetails={this.viewDetails} index={this.state.index}/>;
                break;
            case 'reported':
                return <ReportedItem viewDetails={this.viewDetails} index={this.state.index}/>;
            default:
                return null;
                break;
        }
    }
```
* 第二步：
  在`UnsubmitItem`、`ReportedItem`、`DiagnosisItem`三个组件中：
```js
//接收父组件改变后的props值
componentWillReceiveProps(nextProps) {
  if(nextProps.index == 1){
  //此处的1为index的值，可根据不同的组件设置不同的值。在该例子中UnsubmitItem的index为0，DiagnosisItem的index 为1，ReportedItem的index为2.
  	//此处可以进行setState  等操作
  }
}

shouldComponentUpdate(nextProps,nextState){
  if(nextProps.index == 1){
  	//当点击的tab为当前tab时，重新render
  	return true;
  } else {
  //否则，不重新渲染
  	return false;
  }
}
```


# react-native-ble-manager

- react-native-ble-manager在CSDN上有一篇文档 [React Native BLE蓝牙通信](https://blog.csdn.net/withings/article/details/71378562?locationNum=3&fps=1)，里面讲解的很详细。具体使用情况和使用案例查看[官方文档](https://github.com/innoveit/react-native-ble-manager#methods)。

- 说一说遇到的问题

该组件中的数据发送即`write`使用的是Unicode编码。由于项目中的蓝牙只接受16进制的数据。因为求助于后台，修改了一下源文件:`react-native-ble-manager\android\src\main\java\it\innove`目录下的`BleManager.java`

新建了一个MessageType.java文件,放置一些需要发送给蓝牙的byte类型的常量。

```java
package it.innove;

public interface MessageType {
    public static final byte AD_DATA=0x00;
    public static final byte LEAD_LOST_WARN=0x02;
    public static final byte START_NOTIFY = 0x08;
    public static final byte END_NOTIFY = 0x09;
    public static final byte GET_ELECTRICITY = 0x0D;
    public static final byte GET_DEVICE = 0x0C;
    public static final byte END_FLAG=0x7F;
}
```

BleManager.java中的writeWithoutResponse方法可根据需要做相应调整，此处只修改write方法。

```java
@ReactMethod
public void write(String deviceUUID, String serviceUUID, String characteristicUUID, String message, Integer maxByteSize, Callback callback) {
		Log.d(LOG_TAG, "Write to: " + deviceUUID);

		Peripheral peripheral = peripherals.get(deviceUUID);
		byte decode = 0x00;
		if (peripheral != null){
			switch (message) {
				case "START":
					decode = MessageType.START_NOTIFY; //0x08
					break;
				case "END":
					decode = MessageType.END_NOTIFY; // 0x09
					break;
				case "DEVICE":
					decode = MessageType.GET_DEVICE;
					break;
				case "ELECTRICITY":
					decode = MessageType.GET_ELECTRICITY;
					break;
				default:
					break;
			}
	peripheral.write(UUIDHelper.uuidFromString(serviceUUID), UUIDHelper.uuidFromString(characteristicUUID), new byte[]{decode}, maxByteSize, null, callback, BluetoothGattCharacteristic.WRITE_TYPE_DEFAULT);
		} else
		callback.invoke("Peripheral not found");
}
```
**由于参数message为前台调用write方法时传输的数据，此处改成了String类型，因此前台需要传`START`、`END`等字符串，在MessageType中找到对应的byte类型的数据，再传给蓝牙。**

# 其他

react-native-storage：[github中文文档](https://github.com/sunnylqm/react-native-storage/blob/master/README-CHN.md)

jpush-react-native： [github文档](https://github.com/jpush/jpush-react-native)



