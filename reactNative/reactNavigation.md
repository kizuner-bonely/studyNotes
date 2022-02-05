https://reactnavigation.org/

按照官网教程安装依赖

# AuthStack

```jsx
import { NavigationCaontainer } from '@react-navigation/native'
import { createStackNavigator } from '@react-navigation/stack'

// 已有的页面组件
import { SignIn, CreateAccount } from './Screens'

const AuthStack = createStackNavigator()

export default () => {
  <NavigationContainer>
  	<AuthStack.Navigator>
    	<AuthStack.Screen name="SignIn" component={SignIn} />
      <AuthStack.Screen name="CreateAccount" component={CreateAccount} />
    </AuthStack.Navigator>
  </NavigationContainer>
}

```

在渲染的组件中，可以通过参数获得 `navigation` 数组对象

```js
export const SignIn = ({ navigation }) => {
  const handler = () => navigation.push('createAccount')
}
```

**AuthStack.Screen** 属性

```
name: string
component: JSX.Element
options
	title: string
```



# TabNavigator

自动生成底部按钮组

```jsx
import { NavigationCaontainer } from '@react-navigation/native'
import { createStackNavigator } from '@react-navigation/stack'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'

// 已有的页面组件
import { Home, Profile } from './Screens'

const AuthStack = createStackNavigator()
const Tabs = createBottomTabNavigator()

export default () => {
  <NavigationContainer>
    <Tab.Navigator>
    	<Tab.Screen name="Home" component={Home} />
      <Tab.Screen name="Profile" component={Profile} />
    </Tab.Navigator>
  </NavigationContainer>
}

```



## Tab & Stack

```jsx
import { NavigationCaontainer } from '@react-navigation/native'
import { createStackNavigator } from '@react-navigation/stack'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'

// 已有的页面组件
import { Home, Profile } from './Screens'

const AuthStack = createStackNavigator()
const Tabs = createBottomTabNavigator()
const HomeStack = createStackNavigator()

const HomeStackScreen = () => {
  return (
  	<HomeStack.Navigator>
    	<HomeStack.Screen
        name="Home"
        component={Home}
        options={({ route }) => ({
          title: route.params.name // or something else
        })}
      />
    </HomeStack.Navigator>
  )
}

export default () => {
  <NavigationContainer>
    <Tab.Navigator>
    	<Tab.Screen name="Home" component={HomeStackScreen} />
      <Tab.Screen name="Profile" component={Profile} />
    </Tab.Navigator>
  </NavigationContainer>
}

```





















