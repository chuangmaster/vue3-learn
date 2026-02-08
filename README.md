# Vue 3 完整學習教程

這是一個循序漸進的 Vue 3 學習教程，專注於新語法與特性，特別是 Composition API 和 Composables。

## 目錄

1. [Vue 3 簡介與新特性](#vue-3-簡介與新特性)
2. [開發環境設置](#開發環境設置)
3. [基礎 Composition API](#基礎-composition-api)
4. [響應式系統](#響應式系統)
5. [生命週期鉤子](#生命週期鉤子)
6. [Composables 概念](#composables-概念)
7. [Script Setup 語法糖](#script-setup-語法糖)
8. [進階特性](#進階特性)
9. [實戰項目](#實戰項目)

## Vue 3 簡介與新特性

Vue 3 帶來了許多重要的改進和新特性：

### 🚀 主要新特性
- **Composition API**: 新的組件邏輯組織方式
- **更好的 TypeScript 支持**: 原生 TypeScript 支持
- **性能提升**: 更快的渲染和更小的打包體積
- **Fragment**: 支持多根節點
- **Teleport**: Portal 功能
- **Suspense**: 異步組件處理

### 📦 架構改進
- 模塊化架構
- Tree-shaking 支持
- 更好的 IDE 支持

## 開發環境設置

### 方法 1: 使用 CDN (快速開始)
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Vue 3 學習</title>
</head>
<body>
  <div id="app"></div>
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
  <script>
    // 你的 Vue 代碼
  </script>
</body>
</html>
```

### 方法 2: 使用 Vite 創建項目
```bash
npm create vue@latest my-vue3-app
cd my-vue3-app
npm install
npm run dev
```

## 基礎 Composition API

### 💡 什麼是 Composition API？
Composition API 是 Vue 3 中組織組件邏輯的新方式，它提供了更靈活的代碼組織和更好的邏輯複用能力。

### Options API vs Composition API

#### Options API (Vue 2 風格)
```javascript
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    console.log('組件已掛載')
  }
}
```

#### Composition API (Vue 3 風格)
```javascript
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const count = ref(0)
    
    function increment() {
      count.value++
    }
    
    onMounted(() => {
      console.log('組件已掛載')
    })
    
    return {
      count,
      increment
    }
  }
}
```

### Setup 函數
`setup()` 是 Composition API 的入口點：
- 在組件創建之前執行
- 替代 `data`、`methods`、`computed` 等選項
- 返回的內容會暴露給模板

## 響應式系統

Vue 3 提供了多種響應式 API：

### ref() - 基本響應式引用
```javascript
import { ref } from 'vue'

const count = ref(0)
console.log(count.value) // 0
count.value++ // 觸發響應式更新
```

### reactive() - 響應式對象
```javascript
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  name: 'Vue 3'
})
console.log(state.count) // 0
state.count++ // 觸發響應式更新
```

### computed() - 計算屬性
```javascript
import { ref, computed } from 'vue'

const count = ref(0)
const doubleCount = computed(() => count.value * 2)
```

### watch() - 監聽器
```javascript
import { ref, watch } from 'vue'

const count = ref(0)
watch(count, (newValue, oldValue) => {
  console.log(`count 從 ${oldValue} 變為 ${newValue}`)
})
```

## 生命週期鉤子

Composition API 中的生命週期鉤子：

```javascript
import {
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted
} from 'vue'

export default {
  setup() {
    onBeforeMount(() => {
      console.log('準備掛載')
    })
    
    onMounted(() => {
      console.log('已掛載')
    })
    
    onBeforeUpdate(() => {
      console.log('準備更新')
    })
    
    onUpdated(() => {
      console.log('已更新')
    })
    
    onBeforeUnmount(() => {
      console.log('準備卸載')
    })
    
    onUnmounted(() => {
      console.log('已卸載')
    })
  }
}
```

## Composables 概念

### 🎯 什麼是 Composables？
Composables 是利用 Composition API 封裝和復用**有狀態邏輯**的函數。

### 命名約定
- 以 `use` 開頭（如 `useCounter`、`useFetch`）
- 返回響應式狀態和方法

### 簡單的 Composable 示例
```javascript
// composables/useCounter.js
import { ref } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  
  function increment() {
    count.value++
  }
  
  function decrement() {
    count.value--
  }
  
  function reset() {
    count.value = initialValue
  }
  
  return {
    count,
    increment,
    decrement,
    reset
  }
}
```

### 使用 Composable
```vue
<template>
  <div>
    <p>計數: {{ count }}</p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="reset">重置</button>
  </div>
</template>

<script>
import { useCounter } from './composables/useCounter.js'

export default {
  setup() {
    const { count, increment, decrement, reset } = useCounter(10)
    
    return {
      count,
      increment,
      decrement,
      reset
    }
  }
}
</script>
```

## Script Setup 語法糖

### 🎯 什麼是 `<script setup>`？
`<script setup>` 是 Vue 3.2+ 引入的編譯時語法糖，能以更簡潔的方式編寫 Composition API 代碼。

### 🆕 主要編譯器宏
- **defineProps** - 定義組件 props，支持 TypeScript
- **defineEmits** - 定義組件事件
- **defineModel** - 簡化 v-model 實現 (Vue 3.3+)
- **defineExpose** - 暴露組件方法給父組件
- **defineSlots** - TypeScript 插槽類型支持 (Vue 3.3+)
- **withDefaults** - 為 props 提供默認值

### 基本語法對比

#### 傳統 Composition API
```javascript
export default {
  setup() {
    const count = ref(0)
    
    function increment() {
      count.value++
    }
    
    // 必須手動返回
    return {
      count,
      increment
    }
  }
}
```

#### Script Setup 語法
```javascript
<script setup>
import { ref } from 'vue'

// 直接聲明就可以在模板中使用
const count = ref(0)

function increment() {
  count.value++
}

// 自動暴露，無需 return
</script>
```

### defineModel 語法糖 (Vue 3.3+)
```javascript
// 傳統 v-model 實現
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
const model = computed({
  get: () => props.modelValue,
  set: (val) => emit('update:modelValue', val)
})

// 使用 defineModel 簡化
const model = defineModel()
```

## Script Setup 語法糖

### 🎯 什麼是 `<script setup>`？
`<script setup>` 是 Vue 3.2+ 引入的編譯時語法糖，能以更簡潔的方式編寫 Composition API 代碼。

### 🆕 主要編譯器宏
- **defineProps** - 定義組件 props，支持 TypeScript
- **defineEmits** - 定義組件事件
- **defineModel** - 簡化 v-model 實現 (Vue 3.3+)
- **defineExpose** - 暴露組件方法給父組件
- **defineSlots** - TypeScript 插槽類型支持 (Vue 3.3+)
- **withDefaults** - 為 props 提供默認值

### 基本語法對比

#### 傳統 Composition API
```javascript
export default {
  setup() {
    const count = ref(0)
    
    function increment() {
      count.value++
    }
    
    // 必須手動返回
    return {
      count,
      increment
    }
  }
}
```

#### Script Setup 語法
```javascript
<script setup>
import { ref } from 'vue'

// 直接聲明就可以在模板中使用
const count = ref(0)

function increment() {
  count.value++
}

// 自動暴露，無需 return
</script>
```

### defineModel 語法糖 (Vue 3.3+)
```javascript
// 傳統 v-model 實現
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
const model = computed({
  get: () => props.modelValue,
  set: (val) => emit('update:modelValue', val)
})

// 使用 defineModel 簡化
const model = defineModel()
```

### 多個 v-model 支持
```javascript
<script setup>
// 多個命名的 v-model
const firstName = defineModel('firstName')
const lastName = defineModel('lastName')
const age = defineModel('age', { default: 18 })
</script>

<!-- 使用方式 -->
<UserForm 
  v-model:firstName="user.firstName"
  v-model:lastName="user.lastName"
  v-model:age="user.age"
/>
```

### defineExpose 暴露內部方法
```javascript
<script setup>
const count = ref(0)

function increment() {
  count.value++
}

function reset() {
  count.value = 0
}

// 只暴露必要的方法和數據
defineExpose({
  count,
  reset
  // increment 沒有暴露，父組件無法調用
})
</script>
```

## 進階特性

### 1. Provide / Inject
跨組件層級共享數據：

```javascript
// 父組件
import { provide, ref } from 'vue'

export default {
  setup() {
    const theme = ref('dark')
    provide('theme', theme)
  }
}

// 子組件
import { inject } from 'vue'

export default {
  setup() {
    const theme = inject('theme', 'light') // 默認值為 'light'
    return { theme }
  }
}
```

### 2. 組合式函數的最佳實踐
```javascript
// composables/useFetch.js
import { ref, watchEffect } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)
  const loading = ref(false)
  
  watchEffect(async () => {
    loading.value = true
    error.value = null
    
    try {
      const response = await fetch(url.value)
      data.value = await response.json()
    } catch (err) {
      error.value = err
    } finally {
      loading.value = false
    }
  })
  
  return {
    data,
    error,
    loading
  }
}
```

## 學習路徑建議

### 🎯 學習順序
1. **開始**: 運行 `examples/01-basic-composition-api` 的示例
2. **理解響應式**: 學習 `examples/02-reactive-refs`
3. **掌握計算和監聽**: 探索 `examples/03-computed-watch`
4. **生命週期**: 實踐 `examples/04-lifecycle`
5. **Composables**: 深入 `examples/05-composables`
6. **進階特性**: 挑戰 `examples/06-advanced`
7. **語法糖**: 掌握 `examples/07-script-setup`

### 📚 推薦資源
- [Vue 3 官方文檔](https://vuejs.org/)
- [Vue 3 Composition API 指南](https://composition-api.vuejs.org/)
- [VueUse](https://vueuse.org/) - 實用的組合式函數庫

### 💡 學習技巧
1. **逐步遷移**: 可以在 Vue 3 項目中混用 Options API 和 Composition API
2. **實踐為王**: 多寫代碼，多做項目
3. **閱讀源碼**: 學習優秀的 Composables 實現
4. **社區分享**: 參與討論，分享經驗

## 下一步

歡迎開始探索 `examples/` 目錄中的實例代碼！每個示例都包含詳細的說明和可運行的代碼。

祝學習愉快！🚀