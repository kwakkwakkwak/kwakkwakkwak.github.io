---
title: "[CodeSpitz] Table rendering"
description: "javascript 와 json 데이터로 랜더링 해서 화면에 띄워줌"
categories: 
tags: 
date: 2018-03-16 
comments : true
---


[출처](https://www.facebook.com/groups/codespitz/) 와 [영상](https://www.youtube.com/channel/UCKXBpFPbho1tp-Ntlfc25kA) 입니다.

모든 내용은 위의 출처와 영상에서....



# Page

<objec width="100%" height="400" data='/assets/src/codespitz/20180308/index.html'></object>

* 디자인 패턴으로 알고리즘을 대체하자.

* 데이터 유효성검사(?)와 랜더링하는 부분을 나누어서(변화율에 따라) 작성하는 것이 포인트

# Code

## 실행부분

* 일단 함수(메소드)나 클래스를 만들기전에 어떻게 보여줄지 부터 생각하고 만든다!

```javascript

    const data = new JsonData("71_1.json");
    const renderer = new TableRenderer("#data");
    renderer.render(data);
    
```

## Data 부분

### Data 조상클래스 만들기!

* 데이터 부분 조상클래스를 만들어서 어떠한 데이터가 들어와도 상관 없게 만든다. 

```javascript

const Data = class {
        async getData() {
            throw "getData must override";
        }
    };

```

### 자식클래스 만들기!

* 상관없게 만들지만 처리를 해줄 부분이 필요하다. 

* 그때! 기본적으로 `json`데이터를 받으니까... 위의 `Data`class 를 상속받아서 구현해보자.

* 여기서 `async` 예약어는 원래 `given` `when` `then` 구조의 프로미스 패턴을 (에네르기파 구조) 이쁜 코드로 만들어주는 마법의 예약어이다. 

* 사용법이 어렵....

* 결과값은 `await`로 받는다.

```javascript
const JsonData = class extends Data {
        constructor(data) {
            super();
            this._data = data;

        }

        async getData() {
            if (typeof  this._data === "string") {
                const response = await fetch(this._data);
                return await response.json();
            } else json = this._data;
            return new Info(json);
        }
    };

  
```

### 랜더링 해줄 부분과 관계를 맺어줄 프로토콜을 만들자

* 어떠한 데이터로 들어오던간에 결국에는 json 데이터로 만들어주고, `info`형식으로 맞출거다.

* == 같은말로 어떤 쓰레기값이 들어올지는 모르지만 결국 내가 원하는 VO 형태로 만들어 쓸꺼다.(??)

* 프로토콜 만들어주기!

```javascript

  const Info = class {
        constructor(json) {
            const {title, header, items} = json;
            if (typeof title !== 'string' || !title) throw "invalid title";
            if (!Array.isArray(header) || !header.length) throw "invalid header";
            if (!Array.isArray(items) || !items.length) throw "invalid itmes";
            this._private = {title, header, items};
        }

        get title() {
            return this._private.title
        };

        get header() {
            return this._private.header
        };

        get items() {
            return this._private.items
        };

    };

```


## 테이블 랜더링

### 랜더러 조상클래스 만들기

* 위의 `Data`class 와 같이 조상클래스를 만듭니다. 

* 하지만 여서도 디자인 패턴 구조! `TemplateMethod Patton`

* `render`(TemplateMethod) 와 `_render`(hook)

```javascript
const Renderer = class {
        async render(data) {
            if (!(data instanceof Data)) throw "invalid data type";
            this._info = await data.getData();
            this._render();
        }

        _render() {
            throw "_render must overrided";
        }
    };

```

### TableRenderer 구현

* `Renderer`는 어떻게 어떤 형태로 만들어 줄지 몰라!

* 일단은 `Table` 로 만들꺼야

* 결국 `_render` 만 구현하면 되는거야! 

```javascript

 const TableRenderer = class extends Renderer {
        constructor(parent) {
            if (typeof parent != 'string' || !parent) throw "invalid param";
            super();
            this._parent = parent;
        }

        _render() {
            const parent = document.querySelector(this._parent);
            if (!parent) throw "invaild parent";
            parent.innerHTML = "";
            const [table, caption] = "table,caption".split(",").map(v => document.createElement(v));
            caption.innerHTML = this._info.title;
            table.appendChild(caption);
            table.appendChild(
                this._info.header.reduce(
                    (thead, data) => (
                        thead.appendChild(document.createElement("th")).innerHTML = data, thead),
                    document.createElement("thead"))
            );
            parent.appendChild(
                this._info.items.reduce(
                    (table, row) => (table.appendChild(
                        row.reduce(
                            (tr, data) => (
                                tr.appendChild(document.createElement("td")).innerHTML = data, tr),
                            document.createElement("tr"))
                    ), table),
                    table));
        }
    };
 
```

# Practice

## 1.
     실제 코드를 구현하고 실행하면 예외가 발생한다. 예외의 지점을 찾고 수정하여 완성하라.
     
* 코드로 해보기가 어려워서 생각만 해봤다.

* 기본적으로 `header`가 가지고 있는 길이를 items 의 각요소의 길이와 맞지 않으면 버리는 것

* 이렇게 까지 생각해 봤지만... 코드로 짜기가 여간 쉽지 않았다.. 조금더 공부해서 진행해봐야겠다.. 

## 2.
    지금까지 전개한 객체협력모델에서는 여전히 문제가 남아있다.
    Info는 Data와 Renderer 사이에 교환을 위한 프로토콜인데 Renderer의 자식인 TableRenderer도 Info에 의존적인 상태다. 
    이를 개선하라.

* 문제가 이해는 되지만 어떻게 접근을 해야할지 1도 모르겠다..

* 객체 협력모델... 디자인 패턴을 공부하보면 되는걸까.. 이것도 나중에...