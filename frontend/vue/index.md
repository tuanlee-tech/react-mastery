---
layout: doc
title: Vue
---

# 💚 Vue.js

Progressive JavaScript framework để build user interfaces.

## 📚 Overview

Vue (phát âm /vjuː/, như "view") là framework JavaScript linh hoạt và dễ học. Vue 3 với Composition API mang đến cách viết code hiện đại hơn.

**Topics:**
- [Composition API](/frontend/vue/composition-api) - Modern Vue 3 approach
- [Reactivity System](/frontend/vue/reactivity) - How Vue tracks changes

---

## 🚀 Getting Started

### Create Vue App

```bash
# Create new project
npm create vue@latest

# Follow prompts:
# ✔ Project name: my-vue-app
# ✔ Add TypeScript? Yes
# ✔ Add JSX Support? No
# ✔ Add Vue Router? Yes
# ✔ Add Pinia for state management? Yes
# ✔ Add Vitest for Unit Testing? Yes

cd my-vue-app
npm install
npm run dev
```

### Basic Component

**Counter.vue:**
```vue
<script setup>
import { ref } from 'vue';

const count = ref(0);

function increment() {
  count.value++;
}
</script>

<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<style scoped>
button {
  padding: 8px 16px;
}
</style>
```

---

## 💡 Core Concepts

### Composition API (Vue 3)

```vue
<script setup>
import { ref, computed, watch, onMounted } from 'vue';

// Reactive state
const count = ref(0);
const message = ref('Hello');

// Computed property
const doubled = computed(() => count.value * 2);

// Watcher
watch(count, (newValue, oldValue) => {
  console.log(`Count changed from ${oldValue} to ${newValue}`);
});

// Lifecycle hook
onMounted(() => {
  console.log('Component mounted');
});

// Methods
function increment() {
  count.value++;
}
</script>

<template>
  <div>
    <p>{{ message }}</p>
    <p>Count: {{ count }}</p>
    <p>Doubled: {{ doubled }}</p>
    <button @click="increment">+1</button>
  </div>
</template>
```

### Reactivity

```javascript
import { ref, reactive, computed } from 'vue';

// ref - For primitives
const count = ref(0);
count.value++; // Must use .value

// reactive - For objects
const state = reactive({
  count: 0,
  message: 'Hello'
});
state.count++; // No .value needed

// computed - Derived state
const doubled = computed(() => count.value * 2);
```

### Template Syntax

```vue
<template>
  <!-- Text interpolation -->
  <p>{{ message }}</p>

  <!-- Attributes -->
  <img :src="imageUrl" :alt="imageAlt" />
  
  <!-- Event handling -->
  <button @click="handleClick">Click me</button>
  
  <!-- Two-way binding -->
  <input v-model="text" />
  
  <!-- Conditional rendering -->
  <p v-if="show">Visible</p>
  <p v-else>Hidden</p>
  
  <!-- List rendering -->
  <li v-for="item in items" :key="item.id">
    {{ item.name }}
  </li>
</template>
```

---

## 🎓 Learning Path

### Level 1: Basics
```markdown
- Template syntax
- Reactive data (ref, reactive)
- Event handling
- Conditional rendering (v-if, v-show)
- List rendering (v-for)
- Two-way binding (v-model)
```

### Level 2: Composition API
```markdown
- ref vs reactive
- computed properties
- watchers
- Lifecycle hooks
- Custom composables
```

### Level 3: Advanced
```markdown
- Provide/Inject
- Teleport
- Suspense
- Keep-alive
- Dynamic components
```

### Level 4: Ecosystem
```markdown
- Vue Router - Navigation
- Pinia - State management
- VueUse - Composables library
- Vitest - Testing
```

---

## 🔥 Best Practices

### Component Structure

```vue
<script setup>
// 1. Imports
import { ref, computed } from 'vue';
import ChildComponent from './ChildComponent.vue';

// 2. Props
const props = defineProps({
  title: String,
  count: Number
});

// 3. Emits
const emit = defineEmits(['update', 'delete']);

// 4. State
const localCount = ref(0);

// 5. Computed
const total = computed(() => props.count + localCount.value);

// 6. Methods
function handleClick() {
  emit('update', total.value);
}

// 7. Lifecycle
onMounted(() => {
  console.log('Mounted');
});
</script>

<template>
  <div>
    <h2>{{ title }}</h2>
    <p>Total: {{ total }}</p>
    <button @click="handleClick">Update</button>
    <ChildComponent />
  </div>
</template>

<style scoped>
/* Component styles */
</style>
```

### Reactivity Gotchas

```javascript
// ✅ Good - Use ref for primitives
const count = ref(0);
count.value++;

// ✅ Good - Use reactive for objects
const state = reactive({
  user: { name: 'John' }
});
state.user.name = 'Jane';

// ❌ Bad - Destructuring reactive loses reactivity
const { count } = reactive({ count: 0 });
count++; // Not reactive!

// ✅ Fix - Use toRefs
const state = reactive({ count: 0 });
const { count } = toRefs(state);
count.value++; // Reactive!
```

### Performance

```vue
<script setup>
import { computed, ref } from 'vue';

const items = ref([/* large array */]);

// ✅ Good - Use computed for expensive operations
const filteredItems = computed(() => {
  return items.value.filter(item => item.active);
});

// ❌ Bad - Filter in template
// <div v-for="item in items.filter(i => i.active)">
</script>
```

---

## 📖 Resources

### Official
- [Vue.js Docs](https://vuejs.org/) - Official documentation
- [Vue 3 Composition API](https://vuejs.org/guide/extras/composition-api-faq.html)
- [Vue School](https://vueschool.io/) - Video courses

### Learning
- [Composition API Guide](/frontend/vue/composition-api)
- [Reactivity System](/frontend/vue/reactivity)
- [VueUse](https://vueuse.org/) - Composables collection

### Tools
- [Vue DevTools](https://devtools.vuejs.org/)
- [Vite](https://vitejs.dev/) - Build tool
- [Vitest](https://vitest.dev/) - Testing

---

## 🆚 Vue vs React

### Similarities
- Component-based
- Virtual DOM
- Reactive updates
- Rich ecosystems

### Differences

| Feature | Vue | React |
|---------|-----|-------|
| **Syntax** | Template-based | JSX |
| **State** | ref/reactive | useState |
| **Styling** | Scoped CSS | CSS-in-JS |
| **Learning** | Gentler curve | Steeper curve |
| **Size** | ~33KB | ~42KB |

**Choose Vue if:**
- You prefer template syntax
- You want batteries-included (Vue Router, Pinia official)
- You need gentler learning curve

**Choose React if:**
- You prefer JSX
- You need larger ecosystem
- You want more job opportunities

---

## 💡 Quick Examples

### Form Handling

```vue
<script setup>
import { ref, reactive } from 'vue';

const form = reactive({
  name: '',
  email: '',
  message: ''
});

function handleSubmit() {
  console.log('Form submitted:', form);
}
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <input v-model="form.name" placeholder="Name" />
    <input v-model="form.email" type="email" placeholder="Email" />
    <textarea v-model="form.message" placeholder="Message" />
    <button type="submit">Submit</button>
  </form>
</template>
```

### Data Fetching

```vue
<script setup>
import { ref, onMounted } from 'vue';

const data = ref(null);
const loading = ref(true);
const error = ref(null);

async function fetchData() {
  try {
    const response = await fetch('/api/data');
    data.value = await response.json();
  } catch (e) {
    error.value = e.message;
  } finally {
    loading.value = false;
  }
}

onMounted(fetchData);
</script>

<template>
  <div>
    <p v-if="loading">Loading...</p>
    <p v-else-if="error">Error: {{ error }}</p>
    <div v-else>{{ data }}</div>
  </div>
</template>
```

### Custom Composable

```javascript
// useCounter.js
import { ref } from 'vue';

export function useCounter(initialValue = 0) {
  const count = ref(initialValue);
  
  function increment() {
    count.value++;
  }
  
  function decrement() {
    count.value--;
  }
  
  function reset() {
    count.value = initialValue;
  }
  
  return {
    count,
    increment,
    decrement,
    reset
  };
}
```

**Usage:**
```vue
<script setup>
import { useCounter } from './useCounter';

const { count, increment, decrement, reset } = useCounter(10);
</script>

<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="reset">Reset</button>
  </div>
</template>
```

---

## 🎯 Next Steps

**New to Vue?** → Read [Composition API Guide](/frontend/vue/composition-api)

**Understand basics?** → Learn [Reactivity System](/frontend/vue/reactivity)

**Build a project** → Combine Vue Router + Pinia for full app

**Explore ecosystem** → Check out [VueUse](https://vueuse.org/) composables

---

**Happy Vue-ing! 💚**

[← Frontend](/frontend/) | [Composition API →](/frontend/vue/composition-api/)