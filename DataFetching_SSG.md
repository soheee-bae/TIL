### **정적 생성 방식**

애플리케이션이 배포될때 빌드 시 **(Build Time = `next build`)** 콘텐츠가 한번 생성되고 HTML이 [CDN](https://www.notion.so/SSG-Static-Site-Generation-faae8a3c24a14a61be2248d39190b53c?pvs=21)에 저장된다. Statically generated pages는 각 요청때마다 CDN으로 cached가 되고 재사용된다. 이로써 퍼포먼스 속도가 빨라진다.  

`next dev` 개발과정에서 매 request마다 불린다. 

정적페이지를 만드는데 도와주는 정적함수들 : 

- getStaticProps
- getStaticPaths

장점 : 사용자가 보기전에 미리 렌더링을 해야할때 유용하다. 대략적으로 마케팅 페이지, 블로그 포스트, E-커머스 상품 페이지, Docs 등을 사용하는데 유용합니다.

단점 : 페이지에 자주 업데이트 되는 데이터를 표시하고 요청에 따라 페이지 콘텐츠가 달라지는 경우 유용하지 않다. 이러한 경우 SWR나 SSR를 쓰는걸 추천!! CDN에 캐쉬를 저장하지 않아 속도는 느리지만 항상 최신상태를 유지할 수 있습니다.

---

### `getStaticProps`

Server-side에서만 실행되며 client-side에선 절대 실행되지 않는다. 

페이지 콘텐츠가 외부데이터에 연동된다. 기본적으로 page에서 외부데이터를 가져올때 사용된다. 예를 들면 블로그에서 CMS에 작성한 글을 불러올때 처럼.

기본 예 : 

```jsx
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // By returning { props: { posts } }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

데이터를 가져오는 로직을 다른곳에서 가져올때 : 

- 장점 : 이 로직을 또 다른 페이지에서도 사용가능하다 (깔끔함)

```jsx
// lib/load-posts.js

// The following function is shared with getStaticProps and API routes
// from a `lib/` directory
export async function loadPosts() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts/')
  const data = await res.json()

  return data
}

// pages/blog.js
import { loadPosts } from '../lib/load-posts'

export async function getStaticProps() {
  // Instead of fetching your `/api` route you can call the same
  // function directly in `getStaticProps`
  const posts = await loadPosts()

  return { props: { posts } }
}
```

### 사용시 주의해야 할점!!

- page에서만 가능하다
- `_app` , `_document` , `_error` 에선 가능하지 않다.
- 이유 : React needs to have all the required data before the page is rendered.
- standalond function으로 사용해야한다.

### HTML과 JSON 파일

NextJs가 HTML파일을 빌드타임에 렌더링 할때 JSON파일도 렌더링이 된다. JSON파일은 `next/link` 나 `next/router` 를 이용하여 사용되며 getStaticProps를 사용하는 페이지로 이동할때 JSON파일을 가져온다. 이 파일은 컴포넌트의 프롭으로 사용된다.
