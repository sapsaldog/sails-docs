# Blueprint API

### 개요


**blueprint API**는 blueprint 라우트와 blueprint 액션으로 구성되있으며, 모델과 컨트롤러를 만들때마다 [RESTful JSON API](http://en.wikipedia.org/wiki/Representational_state_transfer)를 사용할 수 있는 내장 로직이다.


예를들면, `User.js` 모델 파일과 `UserController.js` 컨트롤러 파일을 만들면, 단 한줄의 코드 없이도 `/user/create?name=joe`로 방문시에 user를 만들고, `/user`로 방문시 어플리케이션의 user들의 배열을 보여주게된다.

Blueprints는 프로토 타이핑하는데 좋을뿐만 아니라 상속이나, 보호, 확장, 비활성화하는 기능때문에 강력한 제작툴이기도 하다.

##### Blueprint 라우트

blueprints를 활성화하고 'sails lift'로 실행할때, 자동으로 [특정한 라우트를 바인드](./#/documentation/concepts/Routes)하기 위해 프레임워크는 컨트롤러와 모델, 그리고 설정을 살펴보게 된다. 가끔 암시적인 blueprint routes(가끔 “shadows”라고도 불리운다.)는 어플리케이션이 수동으로 config/routes.js파일을 바인드 하지 않고도 특정한 요청에 응답하게 한다. 기본적으로 blueprint routes는 상응하는 blueprint *액션*(아래의 “Blueprint Actions”을 볼것)를 가리키고, 이들 전부는 커스텀 코드로 상속 가능하다.

Sails에는 3가지 종류의 blueprint 라우트가 있다:

+ **RESTful routes**, 경로는 항상 /:modelIdentity 또는 /:modelIdentity/:id를 가진다. 이러한 routes들은 취할 액션을 결정하는데 HTTP “동사”를 이용한다;예를들어 /user에 POST요청을 하면, user를 생성하고, /user/123에 DELETE요청을 하면, 123 primary키를 가지고 있는 user를 지운다. 실제 릴리즈 환경에서는, 허용되지 않은 접근을 막기위해 RESTful routes는 일반적으로 [policies](./#/documentation/concepts/Policies)를 통해 보호해야한다.
+ **Shortcut routes**, 인코딩된 경로를 action으로 취한다. 예를들어 /user/create?name=joe는 user를 생성하는 shortcut이다, 반면 /user/update/1?name=mike는 user #1을 업데이트 한다. 이러한 routes는 오직 GET요청에만 반응한다. Shortcut routes는 개발시에는 유용하나, 일반적으로 배포 환경에서는 비활성화 해야한다.
+ **Action routes**는 자동적으로 custom 컨트롤러 액션들의 라우트를 만들어준다. 예를들어, blueprint action route가 활성화 되어있고 만약 FooController.js파일에 bar 매서드가 있으면, /foo/bar 라우트가 자동적으로 생성이된다. RESTful과 shortcut 라우트와 다르게, 액션 라우트는 컨트롤러에 상응하는 모델파일이 *존재하지 않아도* 동작한다.

어떻게 각각의 blueprint route 타입을 활성/비활성화하는지를 포함한 blueprint 설정 옵션에 대해 더 알고 싶으면 [blueprints subsection of the configuration reference](./#/documentation/reference/sails.config/sails.config.blueprints.html)를 살펴봐라.


##### Blueprint 액션

블루프린트 액션(라우트와 액션을 혼동하지 말것)은 컨트롤러와 모델이 같은 이름을 갖게되면(예. `ParrotController`에는 `Parrot` 모델이 필요하다.) 지정되는 일반적인 액션이다. 어플리케이션에서 기본적인 행동을 생각해보라. 예를 들면,  User.js 모델이 있고, 비어있는 UserController.js 컨트롤러가 있으면 명시하지 않아도 'find', 'create', 'update', 'destroy', 'populate', 'add' 그리고 remove 액션은 코드 한줄없이 암시적으로 존재한다. 

기본으로, blueprint RESTful 라우트와 단축 라우트는 그것들에 상응하는 blueprint액션에 묶이게 된다. 그러나, blueprint액션은 컨트롤 파일(예. `ParrotController.find`)을 만들어서 재정의 할 수 있다. 또 다른 방법으로는, _어플리케이션 어디에서나_ 자신만의 [커스텀 blueprint 액션](./#!documentation/guides/customBlueprints)을 만들어서 블루프린트 액션을 재정의 할 수 있다. (예. `api/blueprints/create.js`)


현재 버전의 세일즈에서는 아래의 blueprint 액션이 탑재되어있다.

+ [find](./#/documentation/reference/blueprint-api/Find.html)
+ [findOne](./#/documentation/reference/blueprint-api/FindOne.html)
+ [create](./#/documentation/reference/blueprint-api/Create.html)
+ [update](./#/documentation/reference/blueprint-api/Update.html)
+ [destroy](./#/documentation/reference/blueprint-api/Destroy.html)
+ [populate](./#/documentation/reference/blueprint-api/Populate.html)
+ [add](./#/documentation/reference/blueprint-api/Add.html)
+ [remove](./#/documentation/reference/blueprint-api/Remove.html)

기본으로, blueprint RESTful 라우트와 단축 라우트는 그것들에 상응하는 blueprint액션에 묶이게 된다. 그러나, blueprint 
유사하게, 이것을 custom [커스텀 blueprint 액션](./#!documentation/guides/customBlueprints)을 만들어서 블루프린트 액션을 상속 할 수 있다. (예. `api/blueprints/create.js`)


### Blueprints 재정의하기

( https://stackoverflow.com/questions/22273789/crud-blueprint-overriding-in-sailsjs 에서 참조하였음. )

Sails v0.10에서 blueprints를 상속 하기위해서는, api/blueprints 폴더를 만들고, 블루프린트 파일을 추가해야한다. (예를들면, find.js, create.js 등등). Sails에서 head start를 위한 기본 blueprints actions hook코드를 볼 수있다.

**주의:** 현재 모든 파일들은 소문자로 되어있어야한다. (기본 액션들은 findOne.js를 포함하지만, /api/blueprints에서는 findone.js로 해야한다.)

또한 custom 블루프린트도 지원하는데, 현재는 자동으로 라우팅이 바운드 되지는 않는다. 만약 /blueprints/foo.js를 만들었다면, /config/routes.js 파일에 라우트를 바인드 해줘야한다. (예를들면):

    GET /myRoute': {blueprint: 'foo'}


### 컨트롤러마다 blueprint를 비활성화 시키기

컨트롤러 정의에서 `_config` 키를 정의하여 각각의 컨트롤러에서 `config/blueprints.js`의 셋팅을 재정의 할수도 있다. 그리고 이 파일의 셋팅값을 재정의한 설정 객체를 할당 할 수 있다.

```
module.exports = {
  _config: {
    actions: false,
    shortcuts: false,
    rest: false
  }
}

```

### 주의사항

> + 현재 이 문서는 HTTP에 초점이 맞추어져 있지만, 블루프린트 API(커스텀 액션과 정책들과같은)는 요청 해석자 덕분에 웹소켓에서도 호환이된다. [browser SDK](./#!documentation/reference/SocketClient/SocketClient.html)에 있는 reference section에서 사용법을 참고해라.
>

<docmeta name="uniqueID" value="blueprintapi170785">
<docmeta name="displayName" value="Blueprint API">
<docmeta name="stabilityIndex" value="2">
