## CSR vs Pre-Rendering

**Next.js는 모든 페이지를 미리 렌더링 한다. (Pre-rendering).**

**Next.js는 최소한의 JavaScript를 이용한 렌더링을 하고 나머지 이루에는 차차 Javscript파일들을 받아와 클라이언트 측에서 Javascript를 해석한다. (Hydration)**

### CSR vs Pre-Rendering

**CSR**는 Client side Rendering 으로 초기 렌더링 작업이 사용자 장치에서 발생한다. 브라우저는 UI를 구성하기 위한 JS와 비어있는 HTML을 받는다. JS파일을 브라우저에서 해석해 랜더링 하는 방식을 사용하는데 오래걸리지만 한번 받아오면 클라이언트 측에서 JS을 해석해 랜더링 하면 되므로 화면 깜빡임 없이 부드러운 사용자 경험을 유지할수 있다. 

**Pre-rendering**은 모든 페이지를 미리 서버에서 렌더링 함으로써 초기 렌더링 작업이 서버에서 JS를 이용해 이루어진다. 렌더링 된 HTML파일은 클라이언트로 전송된다. 랜더링 속도가 빠르지만 깜빡임이 많다.

미리 생성된 HTML은 React과 연결되어 있지 않다 (event handler가 적용 되어 있지 않다). 브라우저가 페이지를 로딩 할때 HTML DOM 요소 위에 한번더 JS가 렌더링 되며 각자 자기 자리를 찾아가며 fully interactive한 페이지가 된다. 이 과정을 (HTML DOM 요소에 뒤늦게 JS가 동작하게 되는 과정)을  **Hydration**라고 한다.  

- SSR (Server Side Rendering)
- SSG (Static Site Generation)
