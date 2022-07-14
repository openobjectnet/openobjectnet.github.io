---
title:  "Vue3 Compostion API 정리"
search: false
categories: 
  - Vue
toc: true  
tags:
  - Vue
author: 김도겸
---
<br>
# Intro
안녕하세요. 김도겸입니다. <br>
Vue3의 핵심인 composition api에 대해 스터디하며, 문법과 기초 이론을 정리해보았습니다.

<br><br>

# 1. Compostion API란?

## 개념
 컴포지션 API는 컴포넌트 내에서 사용하는 특정 기능을 갖는 코드를 유연하게 구성하여 사용할 수 있도록 Vue3 버전에 추가된 함수 기반의 API입니다.    
 API라는 이름을 붙인 것처럼 특정 기능을 갖는 함수를 정의하고 API처럼 사용할 수 있게 해주며, 궁극적인 목적인 코드에 대한 재활용성을 높이고, 코드의 가독성을 높이기 위해 추가된 기능입니다.


## Options API의 문제점 해결
기존 방식인 Options API는 비슷한 기능을 가진 로직들이 옵션에 따라 data, methods, computed 등으로 각각 분리됩니다.   
그렇다 보니 코드가 길어지면 길어질 수록 뭐가 뭔지 잘 모르게 되며 위에서 설명했던 궁극적인 목적인 코드의 가독성 향상에 어긋나게 됩니다.   
그러면서 '규모가 커질 수록 관리가 힘들다'는 것이 Vue의 단점으로 여겨졌습니다.   
이를 보완하기 위해 compostion api에서는 setup()이라는 메서드에 한 덩어리로 코드를 구현하고, 관련된 기능들을 한 부분으로 묶을 수 있습니다.

![Options API vs Composition API](/assets/images/vue3-composition-api/OptionsAPI_vs_CompositionAPI.png)

## 주의할 점
composition api에서 setup()이라는 메서드에 모든 코드를 구현하는 것이 가능합니다.   
분류하는 방법이 조금 애매하다거나 아직 제대로 익히지 못했을때 쉽고 편하게 구현할 수 있습니다.   
하지만 이 방식은 편할 수는 있으나, 가독성이 좋지는 않습니다. composition api의 사용 목적이 무색해지죠.   
따라서 다음과 같이 기능에 따라 하나의 메서드로 묶고, 이 메서드를 호출하는 방식을 권장합니다.   

![method_separation](/assets/images/vue3-composition-api/method_separation.png)   

<br>

# 2. LifeCycle hook & setup()

## 라이프 사이클 훅
options api에서의 lifecycle hook과는 좀 다르게 구성되어 있습니다.   
접두사 "on"을 추가함으로써 컴포넌트의 라이프 사이클 훅에 접근할 수 있고, setup 메서드 내에서 모든 라이프 사이클 훅을 선언할 수 있습니다.

![life cycle compare](/assets/images/vue3-composition-api/lifecycle_compare.png)   
위 사진에서 알 수 있듯이 setup()이라는 메서드가 beforeCreate와 created를 대체합니다.   
따라서 beforeCreate, created 라이프 사이클 훅에서 쓰일 코드는 모두 setup()에서 직접 작성하면 됩니다.   
나머지 life cycle hook도 분리하지 않고 모두 setup() 메서드 내부에서 사용합니다.

> beforeCreate, created 라이프 사이클 훅 사이가 setup이 실행되는 시점입니다.

- <b>setup() 사용 예제</b>
    
    간단한 템플릿을 먼저 구현하고,
    ```html
    <template>
    <button @click="increment">
        {{ state.count }}
    </button>
    </template>
    ```
    
    아래와 같이 setup() 메서드를 작성합니다.
    ```html
    <script>
    import { reactive } from 'vue'

    export default {
    setup() {
        const state = reactive({ count: 0 })

        function increment() {
        state.count++
        }

        // don't forget to expose the function as well.
        return {
        state,
        increment
        }
    }
    }
    </script>
    ```
    추가적으로 아래와 같이 사용하는 것도 가능합니다.
    ```html
    <script setup>
    import { reactive } from 'vue'

    const state = reactive({ count: 0 })

    function increment() {
    state.count++
    }
    </script>
    ```
<br>
  
- <b>부모 컴포넌트와 자식컴포넌트의 life cycle 시점</b>
    
    ![composition api life cycle](/assets/images/vue3-composition-api/composiionAPI_lifecycle.jpg)   
    부모컴포넌트가 마운트되기 시작하는 시점부터 마운트가 끝나는 시점 사이에 자식컴포넌트의 setup()이 호출되고 마운트된다는 것을 알 수 있습니다.

<br>

# 3. 반응형 시스템

반응형 시스템은 데이터가 변경되었을 때 이를 감지하고 반응하여 부가적인 동작을 수행하는 것입니다.   
흔히 엑셀에서 A셀과 B셀의 합을 C셀에 sum함수를 설정하면 A셀, B셀이 변경 시에 이에 반응하여 C셀이 변경되는 것을 예로 들 수 있겠습니다.  

## vue2와 vue3의 반응형 시스템 구현 방식의 차이
 
vue3와 vue2에서 반응형 시스템의 구현 방식이 다릅니다.   
조금 어렵지만 주요 개념이니 참고하는 것이  좋을 것 같습니다.

vue2에선 이 반응형 시스템을 object.defineProperty()로 구현하였습니다.   
이를 활용하여 data() 객체 안에 있는 모든 속성을 순회하며 변경을 감지하고 확인합니다.   
이 방식에서의 문제점은 만약 객체 내의 속성이 추가되거나 배열 내의 원소가 변경될 경우 감지가 되지 않아 
vm.$set()을 사용하여 수정하거나 배열의 경우 push,pop,shift 같은 내장 메서드들에 한하여 반응형 처리를 지원하고 있습니다.
```javascript
var vm = new Vue({
	data: {
		fruit: {
            apple: 1,
            banana: 4,
            orange: 5 
        } // 반응형
	},
    methods: {
        add: function() {
            this.fruit.lemon = 5;  // 속성 동적 추가 불가!
        }
    }

})

```
> 반응성 속성을 동적으로 추가하지 못하므로 빈값으로라도 초기 선언 후에 Vue 인스턴스를 초기화해야 합니다.
<br>

vue3에선 proxy 객체를 활용하여 반응형 시스템을 구현하였습니다.   
값에 접근할 때 실행할 코드를 저장하고 값이 변경될 때마다 저장한 코드를 실행하는 단계로 이루어집니다.   
proxy 객체로 구현되면서 vue2의 문제점였던 부분이 해결되었고, composition api에서는 reactive()라는 메서드를 활용하여 반응형을 사용할 수 있습니다.

```javascript

setup() {
    let fruit = reactive({
        apple: 1,
        banana: 4,
        orange: 5
    });

    function add() {
        fruit.lemon = 5; // 속성 동적 추가 가능!
    }
}


```
> 반응형 시스템은 options api와 composition api의 차이점이 아닌 vue2와 vue3에서의 차이입니다. <br>
> 따라서 vue3에서 options api를 활용하여도 동일하게 작동합니다.<br>
> [vue 반응형 동작 원리 참고](https://ui.toast.com/weekly-pick/ko_20210112)

<br>

# 4. 반응성 API

## reactive() & ref() 
위에서 보여드린 코드 예제와 같이 composition-api에서는 reactive()로 반응형 데이터를 설정하고 구현할 수 있습니다.   
하지만, reactive() 메서드에도 한계가 있습니다. 바로 Array, Object, Map, Set 타입으로만 선언이 가능하다는 것입니다.   
그래서 composition api에서는 reactive()의 제한 사항을 해결하기 위해 모든 타입을 보유할 수 있는 ref()를 제공합니다.   
<br>

ref는 모든 원시 타입 값을 포함한 여러가지 타입의 값을 받을 수 있고, 원본 값은 ref 객체의 value 속성을 통해 접근 가능합니다.   
값을 변경할 대에도 value 속성에 접근하여 조작해야 합니다.   

ref가 여러가지 타입에 해당하는 값을 받을 수 있다고 하니 reactive는 굳이 필요 없는거 아니라고 생각할 수 있지만, 
ref의 값으로 객체가 전달될 경우에는 reactive 메소드를 통해 감지를 수행한다고 합니다.   
모든 데이터를 하나의 reactive 객체로 관리할 지, 원시타입은 ref, 객체는 reactive를 사용할 지는 각자의 기준과 취향에 따라 다를 것 같습니다.

사용 방법은 다음 예제와 같습니다.
```html
<template>
  <div>{{ count }} {{ object.foo }}</div>
</template>

<script>
import { ref, reactive } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const object = reactive({ foo: 'bar' });
    console.log(count.value); // 0

    return {
      count,
      object
    }
  }
}
</script>
```
reactive는 객체의 속성을 호출하듯이 사용하면 되고,
ref 객체는 script 내에서 value 속성을 사용해야만 데이터 접근 및 조작이 가능하고, view에서는 value를 붙이지 않아도 정상적으로 데이터 바인딩이 가능합니다.   
템플릿 내에서 사용할 데이터는 return 객체에 추가해야 합니다. 


## toRef(), toRefs()

toRef를 설명하기 전 먼저 예제 하나를 보도록 하겠습니다.

```html
<script setup>
import { reactive, toRef } from 'vue'

const words = reactive({a:"a",b:"b"})
const msg = words.a
</script>

<template>
  <h1>msg = {{ msg }}</h1>
  <input v-model="msg">
</template>
```
위 예제에서 보시면 reactive로 선언한 객체 내부의 속성을 따로 빼서 변수를 만들어 해당 변수를 템플릿에서 사용합니다.   
이런 방식으로 사용하게 될 경우 msg는 반응성을 잃어버려 데이터 변경이 제대로 반영되지 않습니다.   

toRef는 객체에서 사용할 하나의 속성에 대해 객체와 연결성을 유지한 채로 반응성을 가지도록 하는 것입니다.   
toRef는 해당 객체가 존재하지 않아도, 사용 가능한 ref 객체를 return합니다.
따라서 선택적 (false or null이 오는 경우) 속성을 가진 객체여도 활용 가능합니다.

```html
<script setup>
import { reactive, toRef } from 'vue'

const words = reactive({a:"a",b:"b"})
const msg = toRef(words, 'a')
</script>

<template>
  <h1>msg = {{ msg}}</h1>
  <input v-model="msg">
</template>
```

위와 같이 사용하면 msg도 words.a를 사용하는것과 동일하게 데이터 변경이 정상적으로 반영될 것입니다.

toRefs는 reactive 객체에 포함된 속성에 대한 참조를 생성해줍니다.
```html 
<script setup>
import { reactive, toRefs } from 'vue'

const words = reactive({a:"a",b:"b"})
const {a,b} = toRefs(words)
</script>

<template>
  <h1>msg = {{ a }}</h1>
  <input v-model="a">
</template>
```
위와 같이 사용하면 됩니다. toRef처럼 변수로 선언하는 것은 불가능합니다.

> toRef와 toRefs는 주로 props의 객체의 필요한 속성을 가져와 반응성을 주입해줄 때 사용합니다.

## computed 사용법 변화

compostion api에서와 options api에서 computed를 사용하는 방법에 차이가 있습니다.   
좀 더 간략하고 간단하게 만들 수 있습니다. 아래의 예제를 보도록 하겠습니다.

```javascript
const count = ref(1)
const plusOne = computed(() => count.value + 1)

console.log(plusOne.value) // 2

plusOne.value++ // error
```
변수 선언을 하며 computed를 사용한 후 내부의 return 값을 설정하면 됩니다.
이 또한 ref 객체를 반환하기 때문에 value 속성을 사용해야만 데이터 접근과 조작이 가능합니다.

위 예제 방식은 수정이 불가능한 읽기 전용 방식이고, 수정 가능한 방식으로 바꾸기 위해서는 다음과 같이 사용합니다.

```javascript
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0
```


## watch
watch의 사용법도 변화가 있습니다.   
setup 내부에서 사용하기 위해서 다음과 같이 사용합니다.


```javascript
//Watching a getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)

//Watching a ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})

```
반응성을 가지는 변수를 하나 생성 후 해당 변수를 설정하고, 필요한 행위를 구현하면 됩니다.


## watchEffect

watch의 단순화 버전으로 함수만을 값으로 가지며, 여러 반응 속성을 감시하고 싶고, 이전 값에 신경을 쓰지 않을 때 사용됩니다.   
함수 내부의 있는 여러 반응값을 관찰해야 할 때마다 사용하고 그 중 하나가 업데이트 될 때마다 반응합니다.

```javascript
const user = ref('aaa');
const profile = ref('test1');

watchEffect(() => console.log({ user: user.value, profile: profile.value }))

setTimeout(() => {
  user.value = 'bbb';
  // -> watchEffect 발생 
}, 100)
```

> 이 밖에 다른 반응성 API들이 있으나 자주 쓰이지 않는 기능들인 것 같아 설명을 따로 하지 않고,   
> 추가 설명들은 [해당 사이트](https://vuejs.org/api/reactivity-utilities.html)를 참고하시면 될 것 같습니다.

## template Refs
기존 vue에서는 참고하자 하는 템플릿 요소에 ref="바인딩명" 형식으로 작성한 후, 마운트 이후 this.$refs를 통해 참조할 수 있었습니다.   
하지만 컴포지션 API의 경우 this 컨텍스트가 존재하지 않으므로 조금 다른 방법으로 사용해야 합니다.

![template refs](/assets/images/vue3-composition-api/template_refs.png)

참고하고자 하는 템플릿 요소에 ref 속성을 추가하는 것은 기존과 동일합니다. 이에 따라 동일한 이름을 가진 ref 값을 생성합니다.   
템플릿에 추가한 ref 속성의 키 값과 동일한 이름을 가진 ref가 존재한다면, 해당 요소를 지정된 ref 값에 할당시킵니다.   
마운트 과정에서 할당하므로, 마운트 이후 참조 가능하다는 것을 인지하시고 구현하셔야 합니다.


# 5. props, emit

composition api에서 props와 emit을 사용하는 방식이 바뀌었습니다. 

예제 코드를 보시죠,
```javascript
<template>
</template>

<script lang="ts">
import { defineComponent, ref } from '~/vue-wrapper'

export default defineComponent({
  props: {
    id: String,
    label: String,
    disabled: Boolean,
    checked: Boolean
  },
  setup(props, context) {
    const isChecked = ref(props.checked)
    const toggleCheckBox = (): void => {
      isChecked.value = !isChecked.value
      context.emit('on-change', isChecked.value)
    }

    return { isChecked, toggleCheckBox }
  }
})
</script>
```

props의 경우에는 위 와 같이 object를 따로 만든 후 setup 메서드에 props 인자를 추가시키고, 사용하면 됩니다.   
emit의 경우에는 setup 메서드에서 context 인자를 추가시키고, context.emit을 활용하여 사용하면 됩니다.

디스트럭처링을 사용하여 구현하고 싶을 경우 props는 toRefs를 사용하여 구현해야 props의 반응성이 깨지지 않고 정상적으로 전달됩니다.   
emit은 일반적으로 디스트럭처링해도 문제없습니다.  

```javascript 
import { toRefs } from 'vue'

export default {
  props: {
    items: Array,
    checked: Boolean
  },
  setup(props, { emit }) {
    const isChecked = ref(props.checked)
    const { items } = toRefs(props);
    emit('on-change', isChecked.value);
  }
}
```




# 6. context 인자
context는 컴포넌트 속성 3개를 접근할 수 있는 인자(attrs, slots, emit)입니다.   
emit은 위 파트에서 설명드렸고, 남은 2가지 속성을 소개시켜드리겠습니다.   

- context.attrs

  attrs는 props에 선언되지 않은 추가 요소 속성을 가져올 때 사용합니다.
  ```javascript
  export default {
    props: {
      value: String,
    },
    setup(props, context) {
      console.log(context.attrs)
    },
  }
  ```
  하위컴포넌트의 script 코드를 위과 같이 구현하고,<br>  
  상위컴포넌트에서 아래와 같이 template를 구현하였을 때,
  ```html
  <template>
    <custom-component
        :value="value"
        test="hi"
        @close="close"
      />
  </template>
  ```

  선언된 props 외에 모든 것이 포함되어 proxy 객체를 이루고 그 객체가 log로 출력됩니다.
  ![context attrs log](/assets/images/vue3-composition-api/context_attrs_log.PNG)

<br>

- context.slots

  slots는 슬롯 커스텀 렌더링 시에 사용합니다.   
  ```html
  <template>
    <child-component :level="1"> Hello World </child-component>
  </template>
  ```

  ```javascript
  import { h } from 'vue'
  export default {
    props: {
      level: Number,
    },
    setup(props, context) {
      console.log('here')
      return () =>
        h(
          'h' + props.level,
          {}, // props and attributes: OPTIONAL
          context.slots.default() /* Rendering our default slot */
        )
    },
  }
  ```



