@wymjs/vite-inject-env
===

> 環境變數注入

## 安裝

```shell
$ pnpm i -D @wymjs/vite-inject-env
```

## 使用

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import { injectEnv } from '@wymjs/vite-inject-env'
// import { mergeEnv } from '@wymjs/merge-env'

export default async () => {
  // (可選) 可以與 @wymjs/merge-env 搭配使用
  // const envConfig = await mergeEnv<Record<string, any>, 'development'>({ mode: 'development', dirs: [process.cwd()] }) 
  const envConfig = {
    port: 3030,
    apiPrefix: 'http://localhost:3030',
    vite: {
      title: 'vite-project',
    },
  }
  
  return defineConfig({
    plugins: [
      // 配置插件：此段會取出 envConfig 內的 apiPrefix, vite key-value 傳進應用
      injectEnv(
        { 
          // 傳入要傳到應用中的物件
          env: envConfig, 
          // 指定 env 物件的哪些 key 要傳入到應用中(可選，預設 ['mode', 'vite'])
          propNames: ['apiPrefix', 'vite'] 
        }
      ),
    ],
  })
}



// 在 src 下創建 shims.env-config.d.ts 檔案並寫入
declare module '~env-config' {
  // EnvConfig 改為 vite.config.ts 配置的類型，看你怎麼安排，
  // 最快就是 typeof 後 Pick 出來
  export const envConfig: EnvConfig
}



// 專案任意 ts
// 透過 ~env-config import 進來
import { envConfig } from '~env-config'

envConfig.port       // error 類型錯誤(也確實沒值)
envConfig.apiPrefix  // 'http://localhost:3030'
envConfig.vite.title // 'vite-project'
```
