---
title: react组件通信
date: 2018-08-10
categories: react-native
---

组件通信有三种情况： 父传子，子传父，非父子组件。
由于在项目中使用了redux，所以没有处理非父子组件通信的情况，暂不做说明。

# 父组件--->子组件

父-子组件间的通信方式是借助**props**实现的。即子组件中通过props拿到父组件的数据。

* Child.js
```jsx
export default Child extends Component {
  render() {
    return (
    	<View>
    		<Text>this.props.name</Text>
    	</View>
    )
  }
}
```

* Parent.js
```jsx
export default Parent extends Component {
  render() {
    return (
    	<Child  name={'父组件的数据'}/>
    )
  }
}
```

# 子组件--->父组件
子-父组件间的通信是通过函数实现的，归根究底也是依靠**prop**。子组件通过给父组件的函数传参，从而在父组件里拿到子组件的数据。

* Parent.js
```jsx


export default Parent extends Component {
	getChildData = (data) => {
      console.log(data);//子组件的数据
	}

  render() {
    return (
    	<View>
    		<Child getData={this.getChildData}/>
    	</View>
    )
  }
}
```

* Child.js
```jsx
export default Child extends Component {
  render() {
    return (
    	<TouchableOpacity 
    	// bind只绑定this不执行函数。
    		onPress={this.props.getData.bind(this,'子组件的数据')}
    	>
    		<Text>给父组件传数据</Text>
    	</TouchableOpacity>
    )
  }
}
```

**注意**：
可以看到，在子组件向父组件传递参数时，使用了bind。因为要在点击按钮时给父组件传递参数，若不使用bind，则函数自动执行了。
还可以使用第二种方式：
`onPress={()=>{this.props.getData('子组件的数据')}}`

# 父组件调用子组件的方法

使用`ref`属性。

父组件：
```jsx
class Parent extend Component {

getChildMethod = () =>{
//此处的childShow()既是子组件中的函数
  this.refs.child.childShow();
}

render() {
    return (
    	<View>
    		<Child ref='child'/>
    		<TouchableOpacity
    			onPress={this.getChildMethod}
			>
    			<Text>调用子组件的方法</Text>
    		</TouchableOpacity>
    	</View>
    	
    )
  }
}
```

子组件:
```jsx
class Child extend Component {

childShow = () => {
  console.log('我是子组件的方法');
}

render() {
    return (
    	<View>
    		<Text>我是子组件</Text>
    	</View>	
    )
  }
}
```














