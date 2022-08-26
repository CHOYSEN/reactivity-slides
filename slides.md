---
title: '响应式编程'
---

<div class="text-150px"><span class="text-green-400">响应式</span>编程</div>
<div class="text-100px">Reactivity Programming</div>

---

## 输出是多少？

<br />

<div grid="~ cols-2 gap-4">
<!-- left start -->
<section>
<div>

```ts {all|1-4|6-7|9|all}
var o = {
  x: 1,
  y: 1
}

var sum
sum = o.x + o.y

console.log(sum)
```

</div>

<h2
  class="text-gray-400 -mt-45px font-33px"
  v-click="4"
  v-if="$slidev.nav.clicks >= 4"
  v-motion
  :initial="{ x: 0 }"
  :enter="{ x: 350 }">
  // 2
</h2>
</section>
<!-- left end -->

<!-- right start -->
<section>
<div v-click="5">

```ts
o.x = 2

console.log(sum)
```

</div>

<h2
  class="text-gray-400 -mt-45px font-33px ml-350px"
  v-click="6"
>
  // 2
</h2>
</section>
<!-- right end -->
</div>

<style>
.slidev-code .line{
  --fs: 33px;
  font-size: var(--fs);
  line-height: var(--fs);
  display: block;
}
</style>

---
layout: center
---

## Excel 中的响应式编程

<br />
<video controls="controls" class="h-40vh">
  <source src="https://v3.vuejs.org/images/reactivity-spreadsheet.mp4" type="video/mp4">
</video>

---

## 两种方法让 sum = o.x + o.y?

<br />

<div grid="~ cols-2 gap-7">
<!-- left start -->
<section>
<div v-click="1">

```ts
// type 1

setInterval(() => {
  sum = o.x + o.y
}, 1)


o.x = 2
console.log(sum)
```

</div>
</section>
<!-- left end -->

<!-- right start -->
<section>
<div v-click="2">

```ts
// type 2

function setSum() {
  sum = o.x + o.y
}


o.x = 2
setSum()
console.log(sum)
```

</div>
</section>
<!-- right end -->
</div>

<style>
.slidev-code .line{
  --fs: 33px;
  font-size: var(--fs);
  line-height: var(--fs);
  display: block;
}
</style>

---

## Proxy API

<br />

```ts {all|2-4|5-8|all}
var proxyObject = new Proxy(o, {
  get: function(target, property) {
    return target[property]
  },
  set: function(target, property, newValue) {
    target[property] = newValue
    console.log('setting ' + property + ' = ' + newValue)
  }
})

proxyObject.x = 2 // setting x = 2
```

<style>
.slidev-code .line{
  --fs: 24px;
  font-size: var(--fs);
  line-height: var(--fs);
  display: block;
}
</style>

---

## 响应式 API

<br />

```ts {all|1|3-6|8-10|all}
var o = reactive({ x: 1, y: 1 })

var sum
watchEffect(() => {
  sum = o.x + o.y
})

console.log(sum) // 2
o.x = 2
console.log(sum) // 3
```

<style>
.slidev-code .line{
  --fs: 32px;
  font-size: var(--fs);
  line-height: var(--fs);
  display: block;
}
</style>

---

## 响应式原理

<br />

<div class="flex h-14/15">
<section class="h-full overflow-auto mr-3 w-7/10">

```ts {all|4,5|6,15-18,23|1,27,28|29|4,7-14|30,31|4,17-23|all}
var fn = null

function reactive(o) {
  var dependentMap = new Map()
  return new Proxy(o, {
    get: function (target, property) {
      if (fn != null) {
        var dependents = dependentMap.get(property)
        if (dependents == null) {
          dependents = []
        }
        dependents.push(fn)
        dependentMap.set(property, dependents)
      }
      return target[property]
    },
    set: function (target, property, newValue) {
      target[property] = newValue
      var dependents = dependentMap.get(property)
      if (dependents != null) {
        dependents.forEach(fn => fn())
      }
    }
  })
}

function watchEffect(_fn) {
  fn = _fn
  fn()
  fn = null
}
```

</section>

<section class="w-3/10 h-full overflow-auto">

```ts
var o = reactive({ 
  x: 1,
  y: 1 
})

var sum
watchEffect(() => {
  sum = o.x + o.y
})

console.log(sum) // 2
o.x = 2
console.log(sum) // 3
```

</section>
</div>

<style>
.slidev-code .line{
  --fs: 18px;
  font-size: var(--fs);
  line-height: var(--fs);
  display: block;
}
</style>