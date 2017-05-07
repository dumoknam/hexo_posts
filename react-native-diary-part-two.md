---
title: React Native 일기장 앱 개발 (1) - 글쓰기 버튼
date: 2017-01-05 22:58:50
categories:
- 개발
- React Native
tags:
- React Native
- App
---
일기장 앱을 어떻게 만들지 그려보고, 글쓰기 버튼을 달아보자.

<!-- more -->

__이미지를 클라우드를 쓰다가 직접 올리는 방법으로 바꿨는데 블로그 옮기다가 날려먹었다.. 일기장 포스팅 스크린샷은 없다__

## 목업

일기장 앱의 일기 리스트의 목업은 이렇다

__이미지 없음__ <!--![diary_mockup](Diary.png)-->

제일 위에 연도와 월(2017-01)이 타이틀로 있고, 일기 내용이 리스트로 쭉 이어지는 형태이다.
오른쪽 하단에는 **글쓰기 버튼 +**이 있다.
일단 이 글쓰기 버튼부터 달아보자.

## 글쓰기 버튼 달기

### Diary 프로젝트 생성

`react-native init Diary` 명령어로 프로젝트를 생성하자.
__이미지 없음__ <!--![diary_init](0104_01_init.png)-->

이번 포스팅은 자동 생성된 코드에 버튼만 추가하겠다.
> [NativeBase](http://nativebase.io)나 [react-native-action-button](https://github.com/mastermoo/react-native-action-button)같은 컴포넌트를 활용해도 좋다.

완성된 버튼은 이런 모양이다.
__이미지 없음__ <!--![write_button_fin](0105_01_write_button_fin.png)-->

네이비색 동그란 버튼에 그림자 효과가있고 흰 글씨로 + 가 써있다.
누르면 글쓰는 팝업이 떠야 하는데 그 기능은 다른 포스팅에서...

### WriteButton 컴포넌트 파일 생성

앞으로 생성할 컴포넌트 파일들은 src/components 디렉토리에 두겠다.
우선 디렉토리를 만들고
`mkdir -p src/components`

글쓰기 버튼 컴포넌트를 작성할 파일을 생성하자.
`touch src/components/WriteButton.js`

이런 구조가 된다
__이미지 없음__ <!--![touch_file](0105_02_touch_file.png)-->

에디터로 파일을 열어서 주석에 이름부터 써두자

```javascript src/components/WriteButton.js
/* WriteButton Component - dumok */
```

버튼 Class 를 만들자.
React Native [문서](https://facebook.github.io/react-native/docs/getting-started.html)의 Components를 보면 [Button](https://facebook.github.io/react-native/docs/button.html) 컴포넌트가 있지만, 커스터마이징이 적게 되니 마음에 안들면 [TouchableOpacity](https://facebook.github.io/react-native/docs/touchableopacity.html)를 사용하라고 되어 있다. TouchableOpacity를 써보자. ([TouchableHighlight](https://facebook.github.io/react-native/docs/touchablehighlight.html) 컴포넌트를 사용해도 된다.)

WriteButton.js 파일에 TouchableOpacity를 불러오도록 하자.

```javascript src/components/WriteButton.js
/* WriteButton Component - dumok */
import React, { Component } from 'react';
import {
  TouchableOpacity
} from 'react-native';
```

그리고 WriteButton 컴포넌트 클래스를 만들자.

```javascript src/components/WriteButton.js
/* WriteButton Component - dumok */
import React, { Component } from 'react';
import {
  TouchableOpacity
} from 'react-native';

export default class WriteButton extends Component {
  render() {
    return (
      <TouchableOpacity>
      </TouchableOpacity>
    );
  }
};
```

버튼의 + 는 이미지가 아니라 텍스트로 표현할 것이다.
Text 컴포넌트를 import, render에 추가하자.
```javascript src/components/WriteButton.js
/* WriteButton Component - dumok */
import React, { Component } from 'react';
import {
  Text,
  TouchableOpacity
} from 'react-native';

export default class WriteButton extends Component {
  render() {
    return (
      <TouchableOpacity>
      	<Text>+</Text>
      </TouchableOpacity>
    );
  }
};
```

### 메인에서 WriteButton 컴포넌트 불러오기

WriteButton 컴포넌트를 그려보자.

`index.ios.js` 파일을 열어서
14라인 `import WriteButton from '.src/components/WriteButton';` 과
30라인 `<WriteButton />` 두 줄을 추가하자.
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
import WriteButton from './src/components/WriteButton';

export default class Diary extends Component {
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
        <WriteButton />
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

AppRegistry.registerComponent('Diary', () => Diary);
```

이제 `react-native run-ios`로 시뮬레이터를 실행해보면,
+가 보인다!
> 파일을 수정하고 매번 run-ios를 할 필요는 없다. 시뮬레이터는 처음 한번만 실행하고, 파일을 수정한 뒤 시뮬레이터에서 Cmd + R 키를 눌러 새로고침 하면 바꾼 내용을 확인할 수 있다.

__이미지 없음__ <!--![write_button_01](0105_03_write_button_01.png)-->

이제 이 버튼을 꾸며보자.

### StyleSheet 생성하기

`index.ios.js` 파일에서도 볼 수 있듯이,
스타일을 지정할 때에는 StyleSheet라는 컴포넌트를 사용하면 된다.(웹의 css와 동일한 역할이다)

우선 StyleSheet 컴포넌트를 import 한다.
`TouchableOpacity`,`Text`를 import한 구문 아래에 `StyleSheet`를 추가하자.

StyleSheet에는 두 종류의 스타일 정의가 필요하다.
1. 버튼의 + 글자 스타일 (writeButtonText)
2. 버튼 스타일(writeButton)

#### writeButtonText Style
버튼의 + 텍스트는 흰 색깔과 폰트 크기만 지정하면 된다.
- fontSize: 25
- color: 'white'

#### writeButton Style
버튼에 적용될 스타일은 조금 많은데 종류별로 살펴보면,

버튼은 일단 남색이어야 하고,
- backgroundColor: '#001f3f'
- borderColor: '#001f3f'

적당한 크기에 원 형태(borderRadius가 50%)를 가져야 하며,
- height: 50
- width: 50
- borderRadius: 25

버튼의 위치는 다른 컴포넌트에 독립적인 절대경로로, 화면 오른쪽 하단에 위치한다.
- position: 'absolute'
- bottom: 25
- right: 25

버튼의 텍스트는 가운데 정렬
- alignItems: 'center'
- justifyContent: 'center'

마지막으로 **아래 방향 그림자 효과**를 주어서 버튼이 떠있는 것처럼 보이게 한다
- shadowColor: '#000000'
- shadowOpacity: 0.8
- shadowRadius: 2
- shadowOffset: {  height: 1,  width: 0, }

추가된 스타일 코드를 다 합치면 아래와 같다.
``` javascript src/components/WriteButton.js
// ...기존코드 생략...
import {
  Text,
  TouchableOpacity,
  StyleSheet
} from 'react-native';
// ...기존코드 생략...
const styles = StyleSheet.create({
  writeButton: {
    backgroundColor: '#001f3f',
    borderColor: '#001f3f',
    height: 50,
    width: 50,
    borderRadius: 25,
    alignItems: 'center',
    justifyContent: 'center',
    position: 'absolute',
    bottom: 25,
    right: 25,
    shadowColor: '#000000',
    shadowOpacity: 0.8,
    shadowRadius: 2,
    shadowOffset: {
      height: 1,
      width: 0,
    },
  },
  writeButtonText: {
    fontSize: 25,
    color: 'white'
  }
});
```

### StyleSheet 적용하기
생성한 styles를 적용하려면 컴포넌트마다 style prop에 적용할 스타일을 지정해야 한다.

```javascript
<TouchableOpacity>
<Text>
```
에 각각 
```javascript
<TouchableOpacity style={styles.writeButton}>
<Text style={styles.writeButtonText}>
```
style prop을 지정하면 된다.

여기까지 작성된 코드는 이렇다.
```javascript src/components/WriteButton.js
/* WriteButton Component - dumok */
import React, { Component } from 'react';
import {
  Text,
  TouchableOpacity,
  StyleSheet
} from 'react-native';

export default class WriteButton extends Component {
  render() {
    return (
      <TouchableOpacity style={styles.writeButton}>
        <Text style={styles.writeButtonText}>+</Text>
      </TouchableOpacity>
    );
  }
};

const styles = StyleSheet.create({
  writeButton: {
    backgroundColor: '#001f3f',
    borderColor: '#001f3f',
    height: 50,
    width: 50,
    borderRadius: 25,
    alignItems: 'center',
    justifyContent: 'center',
    position: 'absolute',
    bottom: 25,
    right: 25,
    shadowColor: '#000000',
    shadowOpacity: 0.8,
    shadowRadius: 2,
    shadowOffset: {
      height: 1,
      width: 0,
    },
  },
  writeButtonText: {
    fontSize: 25,
    color: 'white'
  }
});
```

이제 시뮬레이터에서 `Cmd+R`로 화면을 리로드하면, 버튼이 나타나는 것을 확인할 수 있다.

### 글쓰기 버튼에 Alert 적용
버튼은 완성되었고, 누르면 눌러지는 효과가 보이긴 하지만 아무 반응이 없으니 조금 심심하다.
버튼을 눌렀을때 경고창이라도 뜨게 해보자.

다시 [문서](https://facebook.github.io/react-native/docs/touchableopacity.html)를 보면, 버튼이 눌러질때 작동할 함수는 onPress prop으로 지정하는 것을 알 수 있다.
우리도 onPressButton 함수를 만들어 보자.

경고창 컴포넌트를 쓰기 위해 WriteButton.js 파일의 import에 Alert을 추가하고,
파일 맨 아래에 onPressButton 함수를 만들자.
```javascript src/components/WriteButton.js
// ... 기존 코드 생략 ...
import {
  Text,
  TouchableOpacity,
  StyleSheet,
  Alert
} from 'react-native';
// ... 기존 코드 생략 ...
const onPressButton = () => {
  Alert.alert('버튼 눌렀네!?');
}
```

그리고 TouchableOpacity에 onPress prop을 지정해서 버튼을 눌렀을때 onPressButton 함수를 호출하도록 연결시키자.
```javascript src/components/WriteButton.js
// ... 기존 코드 생략 ...
      <TouchableOpacity style={styles.writeButton} onPress={onPressButton}>
// ... 기존 코드 생략 ...
```

완성된 코드는 다음과 같다.
```javascript src/components/WriteButton.js
/* WriteButton Component - dumok */
import React, { Component } from 'react';
import {
  Text,
  TouchableOpacity,
  StyleSheet,
  Alert
} from 'react-native';

export default class WriteButton extends Component {
  render() {
    return (
      <TouchableOpacity style={styles.writeButton} onPress={onPressButton}>
        <Text style={styles.writeButtonText}>+</Text>
      </TouchableOpacity>
    );
  }
};

const styles = StyleSheet.create({
  writeButton: {
    backgroundColor: '#001f3f',
    borderColor: '#001f3f',
    height: 50,
    width: 50,
    borderRadius: 25,
    alignItems: 'center',
    justifyContent: 'center',
    position: 'absolute',
    bottom: 25,
    right: 25,
    shadowColor: '#000000',
    shadowOpacity: 0.8,
    shadowRadius: 2,
    shadowOffset: {
      height: 1,
      width: 0,
    },
  },
  writeButtonText: {
    fontSize: 25,
    color: 'white'
  }
});

const onPressButton = () => {
  Alert.alert('버튼 눌렀네!?');
}
```

### 글쓰기 버튼 Alert 확인 (+ android)

아이폰 시뮬레이터에서 `Cmd + R`로 화면을 리로드하고, + 버튼을 눌러보자. 눌렀네!? 가 뜬다.

안드로이드 시뮬레이터에서도 해보자.
`index.android.js`파일을 열어서 내용을 다 지우고,
`index.ios.js`파일 내용을 모두 복사하여 `index.android.js`로 붙여넣기 한다.
genymotion android 에뮬레이터를 실행하고,
`react-native run-android` 명령어로 앱을 실행하자.

아이폰과 동일한(?) 화면이 나오고,
__이미지 없음__ <!--![android_run](0105_04_android_run.png)-->
버튼도 잘 동작한다.
__이미지 없음__ <!--![android_button_press](0105_05_android_button_pressed.png)-->

사실 아이폰과 안드로이드에는 조금 차이가 있다. 안드로이드는 버튼의 그림자 효과가 없는데, [react native 문서](http://facebook.github.io/react-native/releases/0.40/docs/shadow-props.html)를 보면 shadow prop에 ios 라벨만 붙어있다.
android는 지원하지 않는다는 것인데.. 그래도 누군가는 만들어 두었을테니 구글에서 찾아보는것도 좋겠다.

오늘은 여기까지!
