---
title: 优雅的使用v-model进行双向绑定
date: 2019-01-19 20:53:46
categories: Vue
tags:
  - Vue
description: 使用v-model进行双向绑定
---

> Vue的双向绑定机制使开发Web程序变得非常棘手，比如用户输入的同步，但我们可以使用v-model使其变得简单。
>
> 当数据模型更改时，使用v-model指令会自动更新模板，并在模板变化时同步更新数据模型。

双向绑定是非常强大的功能，如果使用得当，可以显著加快开发过程。因为它降低了模版与数据模型一致的复杂性。

在Vue中，使用v-model指令可以很方便的完成双向绑定。

## 绑定到输入框

在输入框中使用v-model：

```vue
<template>
  <h2>My deepest, darkest question: {{existentialQuestion}}</h2>
  <input v-model="existentialQuestion"/>
</template>
<script>
export default {
  data() {
    return {
      existentialQuestion: 'Am I truly an alligator?'  
    }
  }
}
</script>
```

非常easy，当用户输入时，existentialQuestion属性（和h2元素）就会更新。

## 绑定到复选框和单选按钮

复选框和单选按钮的与输入框非常相似。

复选框绑定的值为true或false，而单选按钮绑定的是其value属性的内容。

**另外，一组复选框可以绑定到单个数组，如果被选中，则会将其value属性的内容放入数组中。**

#### 单复选框示例

```vue
<template>
  <p>You have decided this statement is {{statementIsTrue}}</p>
  <label>
    <input type="checkbox" v-model="statementIsTrue"/>
    Is this statement true?
  </label>
</template>
<script>
export default {
  data() {
    return {
      statementIsTrue: true  
    }
  }
}
</script>
```

#### 多复选框示例

```vue
<template>
  <p>A list of names that rhyme: {{namesThatRhyme.join(', ')}}</p>
  <label>
    <input type="checkbox" value="Daniel" v-model="namesThatRhyme"/>
    Daniel
  </label>
  <label>
    <input type="checkbox" value="Nathaniel" v-model="namesThatRhyme"/>
    Nathaniel
  </label>
  <label>
    <input type="checkbox" value="Hubert" v-model="namesThatRhyme"/>
    Hubert
  </label>
</template>
<script>
export default {
  data() {
    return {
      namesThatRhyme: []  
    }
  }
}
</script>
```

#### 单选按钮示例

```vue
<template>
  <p>You have decided this statement is </p>
  Is this statement true? {{statementIsTrue}}.
  <label>
    <input type="radio" value="Yes" v-model="statementIsTrue"/>
    Yes, it is true.
  </label>
  <label>
    <input type="radio" value="No" v-model="statementIsTrue"/>
    No, not at all.
  </label>
</template>
<script>
export default {
  data() {
    return {
      statementIsTrue: 'Yes'  
    }
  }
}
</script>
```

## 自定义组件实现v-model

v-model内置实现的所有组件都需要接受:value属性，并在用户更改值时发出@input事件。所以自定义组件只需要申明value属性和emit('input')就可以轻松实现v-model。

#### 基本实现

假设一个日期选择器组件，它接收对象中的月份和年份，格式为：`{month：1，year：2017}`。

我们希望该组件有两个输入框，一个用于月份，一个用于年份，并通过v-model更新绑定对象。

自定义组件如下：

```vue
<template>
  <div class="date-picker">
    Month:<input type="number" ref="monthPicker" :value="value.month" @input="updateDate"/>
    Year:<input type="number" ref="yearPicker" :value="value.year" @input="updateDate"/>
  </div>
</template>
<script>
export default {
  props: ['value'],
  methods: {
    updateDate() {
      this.$emit('input', {
        month: this.$refs.monthPicker.value,
        year: this.$refs.yearPicker.value
      })
    }
  }
};
</script>
```

使用组件如下：

```vue
<template>
  <div class="wrapper">
    <date-picker v-model="date"></date-picker>
    <p>
      Month: {{date.month}}
      Year: {{date.year}}
    </p>
  </div>
</template>

<script>
import DatePicker from './DatePicker.vue';
export default {
  data() {
    return {
      date: {
        month: 1,
        year: 2017
      }
    }
  },
  components: {
    DatePicker
  },
})
</script>
```

正如所看到的，它只需要一个:value属性并在更新日期时抛出input事件。

#### 高级用法

vue支持自定义属性名，和抛出事件名。

自定义组件如下：

```vue
<template>
  <div class="date-picker">
    Month:<input type="number" ref="monthPicker" :value="date.month" @input="updateDate"/>
    Year:<input type="number" ref="yearPicker" :value="date.year" @input="updateDate"/>
  </div>
</template>
<script>
export default {
  props: ['date'],
  model: {
    prop: "date", 
    event: "update"
  },
  methods: {
    updateDate() {
      this.$emit('update', {
        month: this.$refs.monthPicker.value,
        year: this.$refs.yearPicker.value
      })
    }
  }
};
</script>
```

使用组件如下：

```vue
<template>
  <div class="wrapper">
    <date-picker v-model="date"></date-picker>
    <p>
      Month: {{date.month}}
      Year: {{date.year}}
    </p>
  </div>
</template>

<script>
import DatePicker from './DatePicker.vue';
export default {
  data() {
    return {
      date: {
        month: 1,
        year: 2017
      }
    }
  },
  components: {
    DatePicker
  },
})
</script>
```

## Tips

* 默认情况下，每次触发输入事件时（即按键或粘贴时）都会执行v-model。如果想等到用户完成输入并且失去焦点再执行v-model，可以使用**v-model.lazy**修饰符让v-model更改触发事件。
* 如果想将用户输入的值转换为数字而不是字符串，可以添加**v-model.number**修饰符。但是只限Number类型，其他类型的转换必须自己处理。
* **v-model.trim**修饰符可以将绑定的字符串中开头或尾随空格删除。但不能与**v-model.number**一起使用。
* v-model 的实现原理只是简单的定义value和抛出input事件。所以，可以轻松地为自定义组件实现v-model。