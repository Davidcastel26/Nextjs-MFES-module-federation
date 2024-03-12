# Nextjs-MFES-module-federation
this is a short cut how to create the set up for MFEs

## This app is being build with MicroFronted Design 

PLEASE:

check the documentation repository for any question [Repo - Here](https://github.com/module-federation/module-federation-examples/tree/master)

Remember
- 🤓 All MFs must be with Nextjs version @13 since MFs Federation are not available for @14 yet
- 😵‍💫 Feeling lost? you need to run:
    next 14 already available but this set only works with next 13, remember this app should be with this next structure /src/pages and does not run with next router
    ```
    <!-- this is #1 -->
     npx create-next-app@13.5.5 shell-App --typescript --tailwind --eslint
    <!-- this is #2 -->
     npx create-next-app@13.5.5 mfe-orders-App --typescript --tailwind --eslint
    ```
- ⚡ Fun fact: This app is runing with [shadcn/ui](https://ui.shadcn.com/docs/installation) for more info
    ```
     npx shadcn-ui@latest init
    ```
- 🔥 Install module federation with this version 7.0.8 into both projects
    ```
    npm i @module-federation/nextjs-mf@7.0.8
    ```
- 🏃🏽‍♂️ Last: this is a must to install into both projects
    ```
    npm install webpack@5.89.0 webpack-cli webpack-dev-server --save-dev
    ```

## Setting up
- On the Remote app add NextFederationPlugin to next.config.js and define modules ( components or pages) that need to be shared.

```js 
const NextFederationPlugin = require('@module-federation/nextjs-mf');

const nextConfig = {
  webpack(config, options) {
    config.plugins.push(
      new NextFederationPlugin({
        name: 'my-shell',
        filename: 'static/shellRemoteEntry.js',
        remotes: {
          cardMfe: 'cardMfe@http://localhost:3001/_next/static/cardMfeRemoteEntry.js',          
        }
      })
      
    );
    return config;
  },
}

module.exports = nextConfig
```
- On the Host app add NextFederationPlugin to next.config.js and specify remotes that should be consumed.


```js
const NextFederationPlugin = require('@module-federation/nextjs-mf');

const nextConfig = {
  webpack(config, options) {
    config.plugins.push(
      new NextFederationPlugin({
        name: 'cardMfe',
        filename: 'static/cardMfeRemoteEntry.js',        
        exposes: {
          './card': './src/components/Card',                  
          // './pages-map': './pages-map.js',
        },        
      })
    );
    return config;
  },
}

module.exports = nextConfig
```

- Now remote modules can be consumed by the Host app using next/dynamic

remember this is into /component/CardMfe.tsx
```tsx
  import dynamic from 'next/dynamic';

 export const CardMfe = dynamic(
  // @ts-ignore
    () => import('cardMfe/card'),
    { 
      ssr: false,
      suspense: true 
    }
  );
```

then we can use the component where we want
```tsx
import { CardMfe } from '@/components/CardMfe'

export default function Home() {
  return (
    <>
      <h1>hola</h1>
      <CardMfe />
    </>
  )
}

```


Teka a look into the [documentation](https://module-federation.io/)

---
⭐️ From [@Davidcastel26](https://github.com/Davidcastel26) & [@kunjolee](https://github.com/kunjolee)
