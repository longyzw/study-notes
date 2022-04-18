# Vite

## 路径别名（ts）

```typescript
// vite.ts

export default defineConfig({
    resolve: {
        alias: {
            "@": resolve(__dirname, "src"), // 设置 `@` 指向 `src` 目录
        },
    }
})
 
// tsconfig.json
{
    "compilerOptions": {
        "paths": {
          	"@/*": ["./src/*"]
        }
    }
}
```

