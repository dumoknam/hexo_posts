---
title: React Native 일기장 앱 개발 (2) - 리스트 뷰
date: 2017-01-14 16:19:20
categories:
  - 개발
  - React Native
tags:
  - React Native
  - App
---

ListView를 써서 일기 목록을 표시하자.

<!-- more -->

## 예제 코드 정리

자동 생성된 코드를 정리할 시간이다.

`index.ios.js` 파일에서 불필요한 코드를 지워버리자.

```javascript index.ios.js
/**
 * React Native Diary App
 * https://dumoknam.github.io
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View
} from 'react-native';
import WriteButton from './src/components/WriteButton';

export default class Diary extends Component {
  render() {
    return (
      <View style={styles.container}>
        <WriteButton />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  }
});

AppRegistry.registerComponent('Diary', () => Diary);
```

달랑 버튼만 남겨 두었다.

## Header

일기 목록 위쪽에 연도를 표시할 컴포넌트 부터 만들어 보자.

컴포넌트 이름은 `DiaryTitleView`으로 정했다.

`touch src/components/DiaryTitleView.js` 명령어로 파일을 생성하고, 아래 내용을 작성하자.

```javascript src/components/DiaryTitleView.js
var React = require('react');
import {
  StyleSheet,
  Text,
  View,
  StatusBar
} from 'react-native';

var headerText = "2017 - 01";

var DiaryTitleView = React.createClass({

  render: function() {
    return (
      <View>
        <StatusBar
          backgroundColor="#001f3f"
          barStyle="light-content"
        />
        <View style={styles.headerContent}>
          <Text style={styles.headerText}>{headerText}</Text>
        </View>
      </View>
    );
  }

});
const styles = StyleSheet.create({
  headerContent: {
    paddingTop: 30,
    paddingBottom: 5,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#001f3f',
    borderColor: '#001f3f',
  },
  headerText: {
    textAlign: 'center',
    color: '#F8F8F8',
    fontSize: 20,
    fontFamily: 'Verdana',
  },
});

module.exports = DiaryTitleView;
```

이전 포스팅에서도 사용했던 `View`, `Text`, `StyleSheet` 컴포넌트 외에, 처음 `import` 되는 `StatusBar`가 보인다.

[`StatusBar`](https://facebook.github.io/react-native/docs/statusbar.html)는 스마트폰 위쪽에 안테나, 시간, 배터리 등이 표시되는 부분을 제어하게 해주는 컴포넌트로,
여기서는 `StatusBar`의 글자색을 흰색으로 하는 `barStyle="light-content"`를 사용하기 위해 `import` 하였다. (`backgroundColor`는 안드로이드만 적용된다.)

지금은 `headerText에` 고정된 값(2017 - 01)을 쓰고 있지만
추후에 날짜를 선택하면 선택한 날짜가 표시되게 변경할 것이다.

`DiaryTitleView.js`파일을 저장했으면, `index.ios.js`에서 불러들이게 하자.

`import`구문을 추가하고, `render`함수의 `WriteButton` 윗줄에 `DiaryTitleView`를 추가하자.

```javascript index.ios.js
import DiaryTitleView from './src/components/DiaryTitleView';
// ...이전 코드 생략...
  render() {
    return (
      <View style={styles.container}>
        <DiaryTitleView />
        <WriteButton />
      </View>
    );
  }
// ...이전 코드 생략...
```

그리고 `react-native run-ios`로 시뮬레이터를 실행하면 2017-01 문자열이 보인다.

![title](title.png)

## ListView

일기 헤더는 완성되었고, 일기 목록을 나타내야 하는데,
문서를 보니 [리스트 뷰](https://facebook.github.io/react-native/docs/listview.html)가 있다. 이걸 써보자.

문서의 예제를 보면 `ListView` 컴포넌트를 쓰기 위해
- `dataSource` 에 나타낼 데이터를 넣고,
- `renderRow` 에 그릴 방법을 넣으면 되는 것 같다.

다른 prop도 많지만 일단 이 두개만 써보기로 한다.

### 임시 데이터
아직 데이터베이스 연동은 구현하지 않았으므로, 임시 일기 데이터 파일을 만들어 두자.

`touch src/components/TempDiaryContents.js`

파일내용은 이렇다.

```javascript src/components/TempDiaryContents.js
var TempDiaryContents = [
  {
    'title': '01 - 10 (화)',
    'content': '오늘은 뭐하지'
  },
  {
    'title': '01 - 09 (월)',
    'content': 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.'
  },
  {
    'title': '01 - 08 (일)',
    'content': '"Lorem ipsum" text is derived from sections 1.10.33 of Cicero\'s De finibus bonorum et malorum.'
  },
  {
    'title': '01 - 06 (금)',
    'content': 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.'
  },
  {
    'title': '01 - 05 (목)',
    'content': '5일이다'
  },
  {
    'title': '01 - 03 (화)',
    'content': '빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥\n빈둥빈둥'
  },
  {
    'title': '01 - 01 (일)',
    'content': '처음이다 일기를 써보자\n쭉쭉 써보자'
  },
];

module.exports = TempDiaryContents;
```

이 `TempDiaryContents`를 `ListView`의 `dataSource`로 사용할 것이다.

내용은 마음대로 써도 되지만 `title`, `content` 항목명은 그대로 두자.

### DiaryListView 컴포넌트

리스트 컴포넌트를 작성하자.

`touch src/components/DiaryListView.js`로 파일을 만들고,

아래 내용을 작성하자.

```javascript src/components/DiaryListView.js
/* DiaryListView Component - dumok */
import React, { Component } from 'react';
import {
  Text,
  ListView,
  View,
  StyleSheet
} from 'react-native';
import TempDiaryContents from './TempDiaryContents';

export default class DiaryListView extends Component {
  constructor(){
    super();
    var dataSource = new ListView.DataSource(
      {rowHasChanged: (r1, r2) => r1 !== r2});
    this.state = {
      dataSource: dataSource.cloneWithRows(TempDiaryContents),
    };
  }

  renderRow(rowData, sectionID, rowID){
    return (
      <View style={styles.container}>
        <View style={styles.textContainer}>
          <Text style={[styles.textBase, styles.title]}>{rowData.title}</Text>
        </View>
        <View style={styles.textContainer}>
          <Text style={[styles.textBase, styles.content]}>{rowData.content}</Text>
        </View>
      </View>
    );
  }

  render() {
    return (
      <ListView
        dataSource={this.state.dataSource}
        renderRow={this.renderRow}
      />
    );
  }
};


var styles = StyleSheet.create({
  container: {
    flex: 1,
    margin: 10,
    padding: 5,
    shadowColor: '#000000',
    shadowOpacity: 0.2,
    shadowRadius: 0.2,
    shadowOffset: {
      height: 1,
      width: 1,
    },
    borderRadius: 5,
  },
  textContainer: {
    marginBottom: 5
  },
  textBase: {
    fontFamily: 'Verdana',
    marginLeft: 10,
    marginRight: 10,
  },
  title: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#555A5F'
  },
  content: {
    fontSize: 14,
    lineHeight: 20,
    color: '#4B4D52'
  }
});
```

`TempDiaryContents` 파일을 `import`하여 `dataSource로` 지정하였고,

`renderRow` 함수를 만들어서 일기의 제목과 내용을 각각 `View` 컴포넌트 안의 `Text` 컴포넌트에 표시하도록 하였다.
`TempDiaryContents`에 `title`, `content`로 저장한 내용은
각각 `rowData.title`, `rowData.content`로 불러오면 된다.

`Text` 컴포넌트의 `style`을 지정하는 부분이 이전과 조금 다른데,
```javascript
<Text style={[styles.textBase, styles.title]}>{rowData.title}</Text>
```
한 컴포넌트에 여러개의 스타일을 지정하고자 할 경우, 위와 같이 배열로 적으면 된다.

### index.ios.js
`DiaryListView`를 메인 화면에 나타나도록 해보자.
`DiaryTitleView`와 방법은 동일하다.
`import`에 추가, `render`에 추가 하면 된다.

`index.ios.js`파일의 전체 코드는 이렇게 된다.

```javascript index.ios.js
/**
 * React Native Diary App
 * https://dumoknam.github.io
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View
} from 'react-native';
import WriteButton from './src/components/WriteButton';
import DiaryTitleView from './src/components/DiaryTitleView';
import DiaryListView from './src/components/DiaryListView';

export default class Diary extends Component {
  render() {
    return (
      <View style={styles.container}>
        <DiaryTitleView />
        <DiaryListView />
        <WriteButton />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  }
});

AppRegistry.registerComponent('Diary', () => Diary);
```

파일을 저장하고 시뮬레이터에 Cmd+R로 리로드하면, 일기 목록이 나온다.

![diary_list](list_fin.png)


물론 스크롤도 잘 된다.

![diary_list_scroll](list_scroll.png)

일기장 목록의 레이아웃은 만들어 졌으니, 다음엔 데이터베이스 연동을 해야겠다.

오늘은 이만!
