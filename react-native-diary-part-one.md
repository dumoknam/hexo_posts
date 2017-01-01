---
title: React Native 일기장 앱 만들기 (1) - Hello World
date: 2017-01-01 23:27:51
categories:
- 개발
- React Native
tags:
- React Native
- App
---

2017년을 맞아 새로운걸 공부해보자!

하여 정한 주제는 React Native.

## 일기장

일기장 앱을 만드는 튜토리얼을 포스팅 할 것이다.

튜토리얼 이라기 보다는.. 공부하고 만들어 본 과정에 대한 정리가 맞겠다.

여튼 튜토리얼에 자주 나오는 예제가 TODO 만들기 인데, 이를 조금 변형하고자 한다.

<!-- more -->

### [React Native](https://facebook.github.io/react-native/)
자바스크립트와 React로 모바일 앱을 만드는 기술이라고 한다.

### [React](https://facebook.github.io/react/)
화면 개발에 사용되는 자바스크립트 라이브러리이다.

React Native의 기반이 되는 React에 대한 포스팅은 [이 블로그](https://velopert.com/reactjs-tutorials)를 추천한다.

## Hello World
환경설정을 하고, 시작은 언제나 그렇듯 Hello World를 찍어보자

#### 설치
우선 [노드](https://nodejs.org)가 필요하다. 이 포스팅을 작성하는 2017년 1월 1일의 최신 LTS 버전인 v6.9.2 를 사용하겠다. [공식사이트](https://nodejs.org)에서 다운로드, 설치하자.

설치 후 `node -v` 커맨드를 입력하면,
![node_version](https://lh3.googleusercontent.com/1ksUkevVsXBUY9pumMT46Kqc1YAsU60_MSbw7tw-uxNPegkrRFdlGb2HeglmbiHzZBbtXKRsMbXzrA=w2560-h1440-no)
6.9.2 버전이 확인된다.


다음은 React Native CLI 설치이다. 노드 설치 후에 `npm install -g react-native-cli` 커맨드로 설치하자(이 포스팅과 동일한 버전을 사용하려면, `npm install -g react-native-cli@2.0.1` 커맨드로 버전을 명시해서 설치하자).
![react-native-cli_version](https://lh3.googleusercontent.com/is7ijoGDhoWX7Q8GKRRwZ3sl06VuIexWYLguNnB5NioMJO2KYRQtgkQkw3NzFN1IPM3bYeFBNYmKfg=w2560-h1440-no)

다음은 `react-native init HelloWorld` 커맨드로 프로젝트를 생성한다.

![react-native_init](https://lh3.googleusercontent.com/RiCLKIP60pwNE3prNqTijCqe-ZK2_6DaNFFO6wevmgrJSh7ZXoJzvt6nomSQtV7MdO-16YOs8OZRcA=w2560-h1440-no)
시간이 조금 걸린다..

프로젝트가 생성되면 앱을 실행하는 명령어를 친절히 알려준다.

![react-native_to_run](https://lh3.googleusercontent.com/QqVY_BZChtFSQuK_dSj9SwWQDBeg982PGeUyW5G6EQayNtEa9RJusEqlbx5v-uVJ9gOG56ig8Flh9A=w2560-h1440-no)

iOS 실행하기: `react-native run-ios` *(맥에서만 가능하다!)*

Android 실행하기: `react-native run-android` *(에뮬레이터 설정이 되어있어야 한다!)*

안드로이드는 에뮬레이터 설정이 필요하고, 맥 사용자라면 위 명령어를 통해 바로 아이폰 앱을 실행할 수 있다.

안드로이드 에뮬레이터 설정은 [이 포스팅](http://kwon-9981.tistory.com/15)을 참고하자.

#### iOS
생성된 코드를 간단히 살펴보고 Hello World가 나오도록 코드를 조금 수정해보자

에디터로 HelloWorld 디렉토리의 index.ios.js 파일을 열어보자 (포스팅에선 [Atom](https://atom.io)을 사용한다)

*안드로이드는 index.android.js 파일을 수정하면 된다.*

```javascript index.ios.js
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 * @flow
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';

export default class HelloWorld extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <Text style={styles.instructions}>
          To get started, edit index.ios.js
        </Text>
        <Text style={styles.instructions}>
          Press Cmd+R to reload,{'\n'}
          Cmd+D or shake for dev menu
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});

AppRegistry.registerComponent('HelloWorld', () => HelloWorld);
```


위 코드는 크게 4 부분으로 구성되어 있다.
1. import: react와 react-native의 컴포넌트들을 불러온다.
2. HelloWorld class: 핵심은 render 함수. 화면에 무엇을 어떻게 그릴지 정의한다. View 안에 Text 를 정의하고 있다.
3. StyleSheet: 화면에 나타날 모양을 정의한다. css다. **이번 포스팅에선 StyleSheet를 생성하지 않고 바로 스타일을 적용할 것이다.**
4. AppRegistry: Helloworld 컴포넌트를 실행하도록 등록하는 작업이다.

미리 써있는 문구 대신 Hello World를 쓰기 위해 우리가 수정해야 할 곳은 2번과 3번이다. 지워버리자.


```javascript index.ios.js
/**
 * Hello World
 * dumok
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';


AppRegistry.registerComponent('HelloWorld', () => HelloWorld);
```


위 코드에서 시작한다. ~~주석만 조금 수정했다~~

- 우선 Hello World 라는 텍스트를 입력해야 한다. `<Text>Hello World!</Text>`
- 이 텍스트가 들어갈 HelloWorld라는 class를 생성해야 하고, `class HelloWorld`
- class는 react의 Component를 상속해야 한다. `extends Component`
- Component는 화면에 그리는 render 함수를 구현해야 한다. `render() { return (...); }`


여기까지 합치면 다음과 같다.

```javascript index.ios.js
/**
 * Hello World
 * dumok
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';

export default class HelloWorld extends Component {
  render() {
    return (
      <Text>Hello World!</Text>
    );
  }
}

AppRegistry.registerComponent('HelloWorld', () => HelloWorld);
```


여기까지 저장하고 실행해보자
`react-native run-ios` *(안드로이드는 `react-native run-android`)*

한참을 기다리면...

아이폰 시뮬레이터에 Hello World!가!

![hello_world_wt](https://lh3.googleusercontent.com/F4T7XwEzH4skhwytEwey-HUnKVhQshAnJAirqmKabOA5Xb_7ClRN-l2zCLhPIB1Qy3zCEGlwqOdGdg=w2560-h1440-no)
그런데 이놈이 한참 위에 딱붙어 있다.. 가린다...

글자에 margin을 줘서 떨어지게 해보자.

`<Text>Hello World!</Text>` 를
```javascript 
<Text style={{ margin:20 }}>Hello World!</Text>
```
로 바꿔서 저장하자.

글자 크기도 좀 바꾸고 싶다면 
```javascript
<Text style={{margin:20, fontSize:15}}>Hello World!</Text>
```
처럼 작성하면 된다.

**CSS에서는 하이픈(-)을 사용하지만, react의 컴포넌트에서 쓸 때는 font-size 대신 fontSize 처럼 CamelCase로 적어야 한다.**

다시 실행하면 `react-native run-ios`

![hello_world_fin](https://lh3.googleusercontent.com/qMY87S99yPMWnFpK4QGAv5vFZePVvqkKk_uPJrwXFmVowcVxXWPWowOwrZnaIrAFPczt6UuphwGIUA=w2560-h1440-no)

글자가 좀 떨어져 나오는 것을 볼 수 있다.

레이아웃이나 스타일에 대해서는 앱을 만들면서 하나씩 알아보도록 하자.

오늘은 이만!
