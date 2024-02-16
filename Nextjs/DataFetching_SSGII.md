### `getStaticPaths`

페이지 경로가 외부데이터에 연동된다.

Next js 는 Dynamic Routes를 쉽게 형성 할수 있다. Dynamic Route를 사용하는 페이지에서 getStaticPaths가 사용된다면 nextjs는 지정된 모든 path를 미리 렌더링 한다.

`getStaticProps`함수는 params를 받아 그에 해당하는 paths를 받아옵니다. 미리 생성된 paths는 캐싱되기 때문에 라우터로 페이지를 이동할 때 굉장히 빠른 랜더링 속도를 보여줍니다.

```jsx
// pages/posts/[id].js

// Generates `/posts/1` and `/posts/2`
export async function getStaticPaths() {
  return {
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    fallback: false, // can also be true or 'blocking'
  };
}

// `getStaticPaths` requires using `getStaticProps`
export async function getStaticProps({ params }) {
  const res = await fetch(`https://.../posts/${param.id}`);
  const post = await res.json();
  return {
    props: { post: {} },
  };
}

export default function Post({ post }) {
  // Render post...
}
```

### 사용시 주의해야 할점!!

- `getStaticProps` 와 같이 써야 한다.
- `getServerSideProps` 와 같이 쓸수 없다.
- 다이나믹 route에서 `getStaticProps` 를 쓰는 곳에만 쓸수있다.
- `page` 파일에서만 가능하다. 컴포넌트 파일은 불가능!
- standalond function으로 사용해야한다.

### 더 나아가서…

`paths` : 미리 렌더링 될 경로를 지정한다. 예를 들면, 만약 `pages/posts/[id].js` 가 `getStaticPaths` 를 사용한다면 nextjs는 `/posts/1` 와  `/posts/2` 를 빌드 되는 동안 형성된다.

```
return {
  paths: [
    { params: { id: '1' }},
    {
      params: { id: '2' },
      // with i18n configured the locale for the path can be returned as well
      locale: "en",
    },
  ],
  fallback: ...
}
```

`getStaticPaths` 는 `fallback` 이란걸 사용해서 production build가 아닌 원할때 페이지가 형성되게끔 조정할수 있다.

빈 arrray를

웹사이트에 정적페이지수가 많을때

```
// pages/posts/[id].js

export async function getStaticPaths() {
  // When this is true (in preview environments) don't
  // prerender any static pages
  // (faster builds, but slower initial page load)
  if (process.env.SKIP_BUILD_STATIC_GENERATION) {
    return {
      paths: [],
      fallback: 'blocking',
    }
  }

  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to prerender based on posts
  // In production environments, prerender all pages
  // (slower builds, but faster initial page load)
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // { fallback: false } means other routes should 404
  return { paths, fallback: false }
}
```

---

---

CDN : content delivery network, geographically distributed group of servers which work together to provide fast delivery of Internet content. It caches content (such as images, videos, or webpages) in proxy servers that are located closer to end users than origin servers. (A proxy server is a server that receives requests from clients and passes them along to other servers.)
