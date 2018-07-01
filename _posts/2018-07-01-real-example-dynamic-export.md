---
layout: post
title: JS 동적인 export의 실제 적용 코드
category: Development
comments: true
---

좀 전에 비동기 모듈의 import, export 에 대한 글을 부족한 영어 실력으로 번역을 했는데, 내친김에 실제 지금 개발하고 있는 리액트 어플리케이션에서 어떻게 사용하고 있는지 적용된 코드를 올려놓으면 나중에 이 부분만 간단히 볼 때 도움이 될 것 같아 작성하게 되었다. 물론 중요한 부분만 추려서.

``` javascript
//App.js
import '...';

@inject('globalStore')
@observer
class App extends React.Component {

  render() {
    const {loginUser} = this.props.globalStore;
    return (
      <div>
        <Router>
          <div>
            <Sidebar {...this.props} routes={sidebarRoutes} loginUser={loginUser}/>
            <div className='main-panel'>
              <Switch>
                <Route exact path='/' component={() => <Home loginUser={loginUser}/>}/>
              </Switch>
            </div>
          </div>
        </Router>
      </div>
    );
  };
}

export default new Promise(async $export => {
  const globalStore = new GlobalStore();
  await globalStore.fetchLoggedInUser(); // 비동기요청을 하고 완료될 때까지 기다림
  const stores = {
    globalStore
  };
  const AppContainer = () => (
    <Provider {...stores}>
      <App/>
    </Provider>
  );
  const module = await Promise.resolve({AppContainer}); // 비동기 요청이 완료된 이후에 module을 export 함
  $export(module);
});
```

```javascript
// index.js
import '...';
import {AppContainer} from 'react-hot-loader';
import App from './App';

const render = Component => {
  ReactDOM.render(
    <AppContainer warnings={false}>
      <Component/>
    </AppContainer>,
    document.getElementById('app-root')
  )
};

App.then(exports => {
  render(exports.AppContainer); // 이 부분을 보면 App은 Promise이고 resolved 되고 나서야, render 가 실행됨을 알 수 있다.
});
```

