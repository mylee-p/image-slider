# webpack-boilerplate

webpack boilterplate 입니다.<br><br> eslint-config-airbnb 가 적용되어있습니다. <br><br> 다운로드 혹은 git clone 하여 `npm install` 실행 후 사용하시면 됩니다.


#### 이미지 슬라이더

<img width="1000" alt="Image" src="https://user-images.githubusercontent.com/89143892/211139891-f44f1721-d49f-459f-a296-19bca594e406.png">

<br />

### 개발환경설정

> Webpack-boilerplate

- [font awesome] 아이콘을 사용하기 위해 라이브러리 설치

```
npm install --save @fortawesome/fontawesome-free
```

> 슬라이드 이미지 관련 설정

```js
//webpack.config.js

module: {
  rules: [
    {
      test: /\.css$/,
      use: [MiniCssExtractPlugin.loader, "css-loader"],
    },
    {
      test: /\.jpeg$/, //jpeg 파일을
      type: 'asset/inline' //webpack에 내장된 로더로 읽어들이겠다.
    }
  ],
},
```

Lodash 문법을 사용해서 입력해준다. <br /> Node.js에서는 require 메서드를 통해 외부 모듈을 가져올 수 있다.

```html
<!-- index.html -->

<div class="slider-wrap" id="slider-wrap">
  <ul class="list slider" id="slider">
    <li>
      <img src="<%= require('./src/image/red.jpeg') %>" />
    </li>
    <li>
      <img src="<%= require('./src/image/orange.jpeg') %>" />
    </li>
    <li>
      <img src="<%= require('./src/image/yellow.jpeg') %>" />
    </li>
    <li>
      <img src="<%= require('./src/image/green.jpeg') %>" />
    </li>
    <li>
      <img src="<%= require('./src/image/blue.jpeg') %>" />
    </li>
    <li>
      <img src="<%= require('./src/image/indigo.jpeg') %>" />
    </li>
    <li>
      <img src="<%= require('./src/image/violet.jpeg') %>" />
    </li>
  </ul>
</div>
```

### next, prev 버튼 개발

```js
//imageSlider.js

export default class ImageSlider {
  // 클래스 변수 일부만 private 하게 사용
  #currentPosition = 0; // 현재에 몇 번째 슬라이더에 있는지를 저장

  #slideNumber = 0; // 슬라이드의 개수를 저장

  #slideWidth = 0; // 슬라이드의 너비를 저장

  sliderWrapEl;

  sliderListEl;

  nextBtnEl;

  previousBtnEl;

  // 초기화해준 값을 constructor에 넣어서
  // 클래스에서 인스턴스를 만들 때 실행시켜주도록 함.
  constructor() {
    this.assignElement();
    this.initSliderNumber();
    this.initSlideWidth();
    this.initSliderListWidth();
    this.addEvent();
  }

  // 엘레먼트들을 찾아서 가지고 있는 assignElement()
  assignElement() {
    this.sliderWrapEl = document.getElementById('slider-wrap');
    this.sliderListEl = this.sliderWrapEl.querySelector('#slider');
    this.nextBtnEl = this.sliderWrapEl.querySelector('#next');
    this.previousBtnEl = this.sliderWrapEl.querySelector('#previous');
  }

  // 초기화 메소드

  // 슬라이드 리스트들이 몇개 있는지 알려주는 메소드
  initSliderNumber() {
    // length를 이용하여 querySelectorAll이 몇 개를 탐색했는지 알 수 있다.
    this.#slideNumber = this.sliderListEl.querySelectorAll('li').length;
  }

  // slideWidth를 초기화하는 메소드
  initSlideWidth() {
    // slideWidth는 sliderWrapEl에 css 1000px을 가지고 오는 코드
    this.#slideWidth = this.sliderWrapEl.clientWidth;
  }

  // 현재 slideList는 float을 써서 List가 옆으로 쭉 이어져 있는 형태
  // 자바스크립트를 이용해 동적으로 리스트를 개수에 따라서 리스트를 설정해주는 로직
  // sliderListWidth를 초기화 하는 메소드
  initSliderListWidth() {
    // 개수 * 높이를 해서 SliderList의 총 Width를 계산해서 다시 sliderListEl에 넣었다.
    // 그러면 css에서 8000px을 넣은 것과 같은 효과가 나타나게 된다.
    this.sliderListEl.style.width = `${this.#slideNumber * this.#slideWidth}px`;
  }

  // 이벤트 추가
  addEvent() {
    // 클릭하면 두번째 파라미터로 넣은 이벤트 핸들러를 실행하는데
    // moveToRight라는 메소드를 따로 만들어서 메소드를 실행하도록 했다.
    // moveToRight 메소드에서 this를 사용할 것이기 때문에 bind(this)를 해준다.
    this.nextBtnEl.addEventListener('click', this.moveToRight.bind(this));
    this.previousBtnEl.addEventListener('click', this.moveToLeft.bind(this));
  }

  moveToRight() {
    // 현재의 위치값을 1 더해주는 로직
    // eslint-config-airbnb에서는 '++'는 사용하지 못하게 되어있다.
    this.#currentPosition += 1;

    // 경계값 처리해주기
    // 현재 슬라이드가 0~6까지 있는데
    // 0에서 -1로 내려갈때나 6에서 7로 올라갈때를 처리해서 슬라이드가 끊김없이 이어지게.
    // 동적으로 만들어줘야하니까 this.sliedNumber를 이용해서
    // 현재 위치가 슬라이드의 개수와 같아지면 this.#currentPosition = 0; 이렇게 처리해주기
    if (this.#currentPosition === this.#slideNumber) {
      this.#currentPosition = 0;
    }

    // 슬라이드를 넘기는 효과를 준다 -1000, -2000 이런식으로
    this.sliderListEl.style.left = `-${
      this.#slideWidth * this.#currentPosition
    }px`;
  }

  moveToLeft() {
    // 현재의 위치값에서 1씩 빼주는 로직
    // currentPosition만 바꿔주면 left에 -값 주는 것을 똑같다.
    this.#currentPosition -= 1;

    // currentPosition이 -1이 되면 currentPosition를 this.#slideNumber - 1; 처리해주기
    if (this.#currentPosition === -1) {
      this.#currentPosition = this.#slideNumber - 1;
    }
    this.sliderListEl.style.left = `-${
      this.#slideWidth * this.#currentPosition
    }px`;
  }
}
```

### 인디케이터 개발

동적으로 움직이는 인디케이터 만들기

```js
//image-slider.js
export default class ImageSlider {
  .
  .
  constructor() {
    .
    .
    //처음상태의 인디케이터 설정
    this.setIndicator();
  }
  assignElement() {
    .
    .
    //인디케이터 탐색
    this.indicatorWrapEl = this.sliderWrapEl.querySelector('#indicator-wrap');
  }
  .
  .
  moveToRight() {
  .
  .
  // 인덱스에 따라서 인덱스가 활성화되는 로직을 붙여준다.
  this.setIndicator();
  }
  moveToLeft() {
  .
  .
  // 인덱스에 따라서 인덱스가 활성화되는 로직을 붙여준다.
  this.setIndicator();
  }
  // 동적으로 인디케이터를 만들어주는 메소드 생성
  createIndicator() {
    // DOM fragment[프래그먼트]를 이용해서 리스트를 한번에 ul태그 안에 넣기
    const docFragment = document.createDocumentFragment();
    // slideNumber를 이용하여 리스트 생성
    for (let i = 0; i < this.#slideNumber; i += 1) {
        const li = document.createElement('li');
        // 리스트에 data-를 스크립트로 지정해줄때는 dataset한 다음에 .내용입력
        // 여기서는 index값을 내려준다.
        li.dataset.index = i;
        // 그리고 document fragment에 저장해놓은데에다가 리스트를 하나씩 넣어준다.
        docFragment.appendChild(li);
    }
    // ul태그 안에서 찾아야되니까.
    // 이렇게 하게되면 리스트들이 생성되서 docFragment안에 넣어졌고 docFragment를 ul의 자식으로 넣었다.
    // docFragment는 렌더가 되지 않고 결과물은 ul태그 안에 우리가 추가한 엘레멘트가 생성되고 중간에 다른 엘레멘트가 껴들지 않는다.
    this.indicatorWrapEl.querySelector('ul').appendChild(docFragment);
  }

  // 인디케이터 활성화
  // 클릭하면 반투명 -> 흰색 버튼으로 색이 변경되는 로직
  setIndicator() {
  // index 활성화 없음 // li(리스트)에 active된걸 찾고 클래스리스트에서 active를 제거
      // 처음에 초기화 될 때는 active된 게 없을수도 있으니까 옵셔널체이닝(?)을 넣었다.
      this.indicatorWrapEl.querySelector('li.active')?.classList.remove('active');
  // index에 따라서 활성화
      // 몇번째를 활성화 해줄 건지, ul태그 안에 li:nth-child로 몇번째를 찾는지 정해준다
      // nth-child는 0부터 시작이 아니라 1부터 시작하기 때문에 + 1을 해준다.
      this.indicatorWrapEl
          .querySelector(`ul li:nth-child(${this.#currentPosition + 1})`)
          .classList.add('active');
  }
}
```

인디케이터를 클릭했을 때 해당 로직으로 이동하는 것 구현 [이벤트 버블링 효과]

```js
//image-slider.js

export default class ImageSlider {
  .
  .
  addEvent() {
    // 인디케이터를 클릭했을 때 해당 로직으로 이동하는 [ 이벤트 버블링 효과 ]
    this.indicatorWrapEl.addEventListener(
      'click',
      this.onClickIndicator.bind(this),
    );
  }
  // indicator-wrap부분에 이벤트를 붙여주고 클릭 한 것 중에 데이터인덱스 값이 있으면
  // 해당 인덱스로 이동하는 로직으로 구현.
  onClickIndicator(event) {
    // 이벤트 타겟의 데이터셋을 가져오는데 인덱스로 저장해서 가져온다.
    // 버튼을 누르면 제대로 콘솔에 찍히지만 그 사이를 누르면 undefined가 출력된다.
    // parseInt() : 현재 번호가 string으로 바뀌어져있는데 number로 바꿔줘야한다.
    // 현재 eslint-config-airbnb 룰에 걸려서 10진수 진법을 사용해야한다. = 원래는 안해도 됨
    const indexPosition = parseInt(event.target.dataset.index, 10);
    // parseInt(undefined)를 넣으면 NaN가 뜨는데 이것을 가려내야하기 때문에 조건문을 작성한다.
    // Number에 스테틱메소드인 isInteger를 이용해 indexPosition에 정수가 있으면 다음을 실행.
    if (Number.isInteger(indexPosition)) {
      // 정수를 currentPosition에 넣고 슬라이드를 넘길 수 있게.
      this.#currentPosition = indexPosition;
      this.sliderListEl.style.left = `-${
        this.#slideWidth * this.#currentPosition
      }px`;
      // position이 바뀌었으니까 다시 업데이트를 해준다.
      // 이제 인디케이터 버튼을 클릭하면 해당 인덱스로 이동하게 된다.
      this.setIndicator();
    }
  }
}
```

### autoplay 개발

슬라이드가 자동으로 넘어가는 로직 개발<br /> 일시정지 버튼을 누르면 슬라이더가 멈추는 로직 개발

```js
//image-slider.js

export default class ImageSlider {
  .
  .
  // 오토플레이 기능을 멈춰야하는 기능을 구현해야하는데 거기에 사용하기 위해
  // setInterval은 id로 저장해둬야한다.
  // 프라이빗 변수로 IntervalId를 만든다.
  #intervalId;

  // 현재 상태가 오토플레이인지 아닌지 판단하기 위해 프라이빗 변수 생성
  // 페이지를 열면 처음에는 오토플레이가 동작하고 있기 때문에 초기값은 true로 해놓는다.
  #autoPlay = true;

  // 클래스 변수가 어떤 일을 하는지 상단에 미리 작성해놔야
  // 나중에 코드를 리팩토링 할 때나 협업을 할 때 알아보기 용이하다.
  sliderWrapEl;
  .
  .
  assignElement() {
    .
    .
    // 페이지가 떴을 때 오토플레이가 동작하는 기능 구현
    this.controlWrapEl = this.sliderWrapEl.querySelector('#control-wrap');
  }
  .
  .
  addEvent() {
    // intervalId을 가지고 오토플레이가 멈추고 다시 재생하는 이벤트 구현
    this.controlWrapEl.addEventListener('click', this.togglePlay.bind(this));
  }
  // html 문서에 data- 속성을 미리 넣어뒀다. data-status="pause", data-status="play"
  // 이를 활용해 재생해야되는지 멈춰야되는지를 판단하는 메소드 구현.
  // 현재 controlWrap에 이벤트를 넣어놨기 때문에 버블링을 이용하여 부모에 한번에 이벤트를 걸면된다.
  togglePlay(event) {
    if (event.target.dataset.status === 'play') {
      // status가 tute이면 플레이버튼이 눌린 상태라고 인식하고
      // controlWrapEl.classList에 play 클래스를 넣을 것 이고
      // controlWrapEl.classList에 pause 클래스가 있으면 삭제할 것이다.
      this.#autoPlay = true;
      this.controlWrapEl.classList.add('paly');
      this.controlWrapEl.classList.remove('pause');
      // 위에는 버튼만 구현했고 동작하는 기능을 불러온다.
      this.initAutoplay();
    } else if (event.target.dataset.status === 'pause') {
      // status가 pause인 경우에는 멈추게 되니까 autoPlay가 false가 된다.
      this.#autoPlay = false;
      // 위와 반대로 동작해야하니까 add와 remove를 변경해준다.
      this.controlWrapEl.classList.remove('paly');
      this.controlWrapEl.classList.add('pause');
      // pause를 누르면 autoPlay를 멈춰야하니까 clearInterval을 이용해서 멈추게한다.
      clearInterval(this.#intervalId);
    }
  }
  .
  .
  // 3초 interval 즉, autoPlay가 계속 작동되고 있는 상태에서
  // 화살표버튼으로 다음 슬라이드로 넘길 시 3초가 안된 상태에서 다음 슬라이드로 넘어가게된다.
  // 슬라이드를 중간에 넘겨버리지 못하게 처리해주기
  // moveToRight(), moveToLeft()에서 처리해주기
  moveToRight() {
    .
    .
    // pause를 누른 상태에서 버튼을 클릭했을 때 setInterval이 시작되면 안되니까
    // 현재 상태가 play인지 pause인지 즉, autoPlay 상태인지 아닌지 알고
    // autoPlay일 경우에만 해당 로직을 실행시킬 수 있게 조건문으로 감싸준다.
    if (this.#autoPlay) {
      // moveToRight()를 누르게 되면 intervalId가 있다면 얘를 멈추고
      // 즉, clearInterval 하고 다시 setInterval을 시작해주는 로직
      // 이렇게 되면 setInterval이 돌고있는데 moveToRight를 눌렀다면
      // 뒤에서 돌고 있는 setInterval은 멈추게 되고 우리가 버튼을 누름으로 인해서
      // 다음 슬라이드로 넘어가게되고 그 상태에서 다시 setInterval을 시작하게된다.
      clearInterval(this.#intervalId);
      this.#intervalId = setInterval(this.moveToRight.bind(this), 3000);
  }
  moveToLeft() {
    .
    .
    // moveToRight()에서 한 것 같이 똑같이 로직을 구성해준다.
    if (this.#autoPlay) {
      clearInterval(this.#intervalId);
      this.#intervalId = setInterval(this.moveToRight.bind(this), 3000);
    }
    this.setIndicator();
  }
  .
  .
}
```
