---
title: 如何动态加载Vuex模块
date: 2019-05-26 23:16:16
categories: Vue
tags:
  - Vue
description: 如何动态加载Vuex模块
---

> Vuex是一种易于使用且高性能的解决方案，可以处理状态管理。它使管理大规模Vue.js应用程序变得轻而易举，它通过暴露Store来改变State，使我们的程序更加具有可测试性。

我们可以在Vuex中自定义Store，例如：

```javascript
const dogs = {
  state: {
    data: []
  },
  mutations: {
    addDog(state, dog) {
      state.data.push(dog)
    }
  }
}

const store = new Vuex.Store({
  modules: {
    dogs
  }
});
```

一般来说，大型应用中业务较复杂，模块会有好多个，我们可以根据业务分模块，并在调用新的Vuex.Store时组合在一起。

这样可以让我们的业务逻辑独立开来，不用在耦合在一起，每个模块，都可以有自己的Component和Store。

通常，这是应用程序中常见的可重用模块的情况。例如，提供一些通知组件的通知模块和扩展应用程序商店的商店模块，添加一个可从应用程序中的任何位置访问的新模块。

我们的模块还可以复用，例如，实现一个可复用的通知模块。

### 动态添加模块到Store

首先我们创建一个模块目录，起名notifications

接着，添加一个带有Vuex模块的state.js：

```javascript
export default {
  state: [],
  mutations: {
    addNotification(state, notification) {
      state.push(notification);
    }
  }
};
```

然后创建一个Notifications.vue文件，导入state.js。然后，通过$ store访问实例变量，我们实现一个测试方法用于获取状态并提交addNotification的Vuex存储：

```vue
<template>
  <div>
    <p v-for="notification in notifications">
      {{notification}}
    </p>
    <button @click="addHey">Add Hey!</button>
  </div>
</template>

<script>
import state from "./state";

export default {
  computed: {
    notifications() {
      return this.$store.state.notifications;
    }
  },
  methods: {
    addHey() {
      this.$store.commit("addNotification", "Hey!");
    }
  }
};
</script>
```

当使用组件时，通知Vuex模块会自行添加，但我们还可以动态添加Vuex模块，使用Store的实例属性$ store.registerModule：

```javascript
import state from "./state";

export default {
  // ...
  created() {
    this.$store.registerModule("notifications", state);
  }
};
```

现在，使用Notifications组件时将注册该模块。

### 小结

大型应用程序中的Vuex存储通过不同业务模块可以很方便的进行解耦，使我们的业务模块独立，并具有可测试性。动态注入Vuex模块，可以提高应用的扩展性和可维护性。

