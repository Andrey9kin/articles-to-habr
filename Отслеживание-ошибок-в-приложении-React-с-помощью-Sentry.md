Отслеживание ошибок в приложении React с помощью Sentry

![](https://habrastorage.org/webt/-t/ew/vi/-tewvivn2k2sfdneybtnpssi06s.png)

Сегодня я расскажу вам об отслеживании ошибок в реальном времени в приложении React. Приложение внешнего интерфейса обычно не используется для отслеживания ошибок. Некоторые компании часто откладывают отслеживание ошибок, возвращаясь к нему после документации, тестов и прочего. Однако, если вы можете изменить свой продукт в лучшую сторону, то просто сделайте это!



### **1. Зачем вам нужен Sentry?**

Я предполагаю, что вы заинтересованы в отслеживании ошибок в процессе производства

Вы думаете, что этого недостаточно? 😄

Хорошо, давайте посмотрим на детали.



**Основные причины использования Sentry для разработчиков:**

- Позволяет избавляться от рисков при развертывании кода с ошибками
- Помощь QA в тестировании кода
- Получение быстрых уведомлений о проблемах
- Возможность быстрого исправления ошибок
- Получение удобного отображения ошибок в админ-панели
- Сортировка ошибок по сегментам пользователя / браузера

**Основные причины для CEO / Lead проекта**

- **Экономия денег (Обращаю внимаю что Sentry можно установить на ваших серверах)**
- Получение отзывов пользователей
- Понимание того, что не так с вашим проектом в режиме реального времени
- Понимание количества проблем, возникающих у людей с вашим приложением
- Помощь в поиске мест, где ваши разработчики допустили оплошность

Я думаю, что разработчики были бы заинтересованы в этой статье в первую очередь. Вы также можете использовать этот список причин, чтобы убедить начальство интегрировать Sentry.

💡 Будьте осторожны с последним пунктом в списке для бизнеса. 😄

Вы уже заинтересованы?

![](https://habrastorage.org/webt/nt/8k/9e/nt8k9elciiwzwczfvysxh48_y7m.gif)

### Что такое Sentry?

Sentry – это приложения для отслеживания ошибок с открытым исходным кодом, которое помогает разработчикам отслеживать, исправлять сбои в режиме реального времени. Не забывайте и о том, что приложение позволяет повышать эффективности и улучшать пользовательское использование. Sentry поддерживает JavaScript, Node, Python, PHP, Ruby, Java и другие языки программирования.

![](https://habrastorage.org/webt/1x/ab/or/1xaboredev5ekhehmvwxmkuhbtk.gif)

### **2. Войдите и создайте проект**

- Откройте ваш Sentry аккаунт. Возможно, вам придется войти в систему. (Обращаю внимаю что Sentry можно установить на ваших серверах)
- Следующий шаг создать проект
- Выберите ваш язык из списка. (Мы собираемся выбрать React. Нажмите «Создать проект»)

![](https://habrastorage.org/webt/bt/_x/qf/bt_xqfkh5sjtl-t_xh-o_wbriau.gif)

Настройте ваше приложение. Базовый пример, как интегрировать Sentry в контейнер, вы можете увидеть ниже:

```react

import * as Sentry from '@sentry/browser';
// Sentry.init({
//  dsn: "<https://63bbb258ca4346139ee533576e17ac46@sentry.io/1432138>"
// });
// should have been called before using it here
// ideally before even rendering your react app 

class ExampleBoundary extends Component {
    constructor(props) {
        super(props);
        this.state = { error: null };
    }

    componentDidCatch(error, errorInfo) {
      this.setState({ error });
      Sentry.withScope(scope => {
        Object.keys(errorInfo).forEach(key => {
          scope.setExtra(key, errorInfo[key]);
        });
        Sentry.captureException(error);
      });
    }

    render() {
        if (this.state.error) {
            //render fallback UI
            return (
              <a onClick={() => Sentry.showReportDialog()}>Report feedback</a>
            );
        } else {
            //when there's not an error, render children untouched
            return this.props.children;
        }
    }
}
```



В Sentry есть полезный Мастер, который поможет вам понять, что вы должны делать дальше. Вы можете выполнить следующие шаги. Я хочу показать вам, как создать первый обработчик ошибок. Отлично, мы создали проект! Перейдем к следующему шагу



### **3. Интеграция React и Sentry**

Вы должны установить npm пакет в ваш проект.

```
npm i @sentry/browser
```

Инициализируйте Sentry в вашем контейнере:

```
Sentry.init({
 // dsn: #dsnUrl,
});
```

DSN находится в Projects -> Settings -> Client Keys. Вы можете найти клиентские ключи в строке поиска.

![](https://habrastorage.org/webt/uo/nt/th/uontthv64gezpslxvaumxcipe9u.gif)

```
componentDidCatch(error, errorInfo) {
  Sentry.withScope(scope => {
    Object.keys(errorInfo).forEach(key => {
      scope.setExtra(key, errorInfo[key]);
    });
    Sentry.captureException(error);
 });
}
```



### **4. Отслеживание первой ошибки**

Я например использовал простое музыкально приложение с API Deezer. Вы можете видеть это [здесь](https://nozhenkod.github.io/sentry-music-app/). Нам нужно создать ошибку. Одним из способов является обращение к свойству "undefined"

Мы должны создать кнопку, которая вызывает **console.log** с **user.email**. После этого действия мы должны получить сообщение об ошибке: *Uncaught TypeError (не удается прочитать свойство из неопределенного `email`)* из-за отсутствия объекта пользователя. Вы также можете использовать **исключение Javascript**.

```javascript
<button type="button" onClick={() => console.log(user.email)}>   
  Test Error button 
</button>
```



Весь контейнер выглядит так:

```
import React, { Component } from "react";
import { connect } from "react-redux";
import { Input, List, Skeleton, Avatar } from "antd";
import * as Sentry from "@sentry/browser";
import getList from "../store/actions/getList";

const Search = Input.Search;

const mapState = state => ({
  list: state.root.list,
  loading: state.root.loading
});

const mapDispatch = {
  getList
};

class Container extends Component {
  constructor(props) {
    super(props);

    Sentry.init({
      dsn: "https://fc0edcf6927a4397855797a033f04085@sentry.io/1417586",
    });
  }

  componentDidCatch(error, errorInfo) {
    Sentry.withScope(scope => {
      Object.keys(errorInfo).forEach(key => {
        scope.setExtra(key, errorInfo[key]);
      });
      Sentry.captureException(error);
    });
  }
  render() {
    const { list, loading, getList } = this.props;
    const user = undefined;
    return (
      <div className="App">
        <button
          type="button"
          onClick={() => console.log(user.email)}
        >
          test error1
        </button>
        <div onClick={() => Sentry.showReportDialog()}>Report feedback1</div>
        <h1>Music Finder</h1>
        <br />
        <Search onSearch={value => getList(value)} enterButton />
        {loading && <Skeleton avatar title={false} loading={true} active />}
        {!loading && (
          <List
            itemLayout="horizontal"
            dataSource={list}
            locale={{ emptyText: <div /> }}
            renderItem={item => (
              <List.Item>
                <List.Item.Meta
                  avatar={<Avatar src={item.artist.picture} />}
                  title={item.title}
                  description={item.artist.name}
                />
              </List.Item>
            )}
          />
        )}
      </div>
    );
  }
}

export default connect(
  mapState,
  mapDispatch
)(Container);
```



После интеграции этой кнопки вы должны протестировать ее в браузере.

![](https://habrastorage.org/webt/kh/5u/uu/kh5uuufshgbzeqeivguoon9lru0.gif)

У нас есть первая ошибка

![](https://habrastorage.org/webt/0u/qy/he/0uqyhehsi9rlogf6acfi7tukfie.png)

Whoo-hoo! 🎉 🎉 🎉

![](https://habrastorage.org/webt/hj/o3/wb/hjo3wbg_rsiviz22-glusyr8rng.gif)

Если вы нажмете на ошибку заголовка, вы увидите трассировку стека.

![](https://habrastorage.org/webt/pz/nz/je/pznzjeyxt3f9xrv4ua4hnro5mam.png)

Сообщения выглядят плохо. Конечно, мы видели сообщения об ошибках, не понимая, где этот код. По умолчанию речь идет об исходной карте в ReactJS, потому что они не настроены.

Я также хотел бы предоставить инструкции по настройке исходной карты, но это сделало бы эту статью намного длиннее, чем я планировал.

Вы можете изучить эту тему [здесь](https://docs.sentry.io/platforms/javascript/?platform=react#source-maps). Если вы заинтересованы в этой статье, [Dmitry Nozhenko](https://medium.com/@dmitrynozhenko) опубликует вторую часть об интеграции source map. Итак, нажимайте больше лайков и подписывайтесь на [Dmitry Nozhenko](https://medium.com/@dmitrynozhenko), чтобы не пропустить вторую часть.




**5. Использование** **Sentry** **с конечной точкой** **API**

Окей. Мы рассмотрели исключение javascript в предыдущих пунктах. Однако, что мы будем делать с ошибками XHR?

Sentry также имеет пользовательскую обработку ошибок. Я использовал его для отслеживания ошибок api.

```
Sentry.captureException(err)
```

Вы можете настроить имя ошибки, уровень, добавить данные, уникальные пользовательские данные с помощью вашего приложения, электронной почты и т. д.

```
superagent
  .get(`https://deezerdevs-deezer.p.rapidapi.com/search?q=${query}`)
  .set("X-RapidAPI-Key", #id_key)
  .end((err, response) => {
    if (err) {
      Sentry.configureScope(
        scope => scope
          .setUser({"email": "john.doe@example.com"})
          .setLevel("Error")
      );
      return Sentry.captureException(err);
    }

    if (response) {
      return dispatch(setList(response.body.data));
    }
  });
```

Я хотел бы использовать общую функцию для API catch.

```
import * as Sentry from "@sentry/browser";

export const apiCatch = (error, getState) => {
  const store = getState();
  const storeStringify = JSON.stringify(store);
  const { root: { user: { email } } } = store;

  Sentry.configureScope(
    scope => scope
      .setLevel("Error")
      .setUser({ email })
      .setExtra("store", storeStringify)
  );
	// Sentry.showReportDialog(); - If you want get users feedback on error
  return Sentry.captureException(error);
};
```

Импортируйте эту функцию в вызов api.

```
export default query => (dispatch, getState) => {
  superagent
    .get(`https://deezerdevs-deezer.p.rapidapi.com/search?q=${query}`)
    .set("X-RapidAPI-Key", #id_key)
    .end((error, response) => {
      if (error) {
        return apiCatch(error, getState)
      }

      if (response) {
        return dispatch(setList(response.body.data));
      }
    });
};
```

Давайте проверим методы:

- **setLevel** позволяет вставить ошибку уровня в панель мониторинга sentry. Он имеет свойства — ‘fatal’, ‘error’, ‘warning’, ‘log’, ‘info, ‘debug’, ‘critical’).
- **setUser** помогает сохранить любые пользовательские данные (id, адрес электронной почты, план оплаты и т. д.).
- **setExtra** позволяет задать любые данные, которые вам нужны, например, магазин.

Если вы хотите получить отзывы пользователей об ошибке, вам следует использовать функцию showReportDialog.

```
Sentry.showReportDialog();
```


Вывод:

Сегодня мы описали один из способов интеграции Sentry в приложение React.

