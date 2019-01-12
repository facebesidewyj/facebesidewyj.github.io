---
title: 如何使用Jest测试Vue
date: 2019-01-12 21:52:46
categories: Vue
tags:
  - Vue
description: 使用Jest测试Vue
---

> 测试Vue项目的重要性不应该被低估，Jest框架使得测试更加容易。

由于我们在Vue环境中，我们将使用一个测试项目来讲述如何使用Jest测试。

## 项目初始化

我们使用Vue CLI来初始化一个测试Vue项目。

* 安装Vue CLI：

```bash
npm install -g @vue/cli
```

* 创建测试Vue项目

```bash
vue create testing-vue
cd testing-vue
code .
npm run serve 
```

## 测试

现在我们已经使用Jest生成了Vue项目，我们可以切换到tests/unit目录。

在这个目录里面，我们创建一个名为example.spec.js的文件：

```
import { shallowMount } from "@vue/test-utils";
import HelloWorld from "@/components/HelloWorld.vue";

describe("HelloWorld.vue", () => {
  it("renders props.msg when passed", () => {
    const msg = "new message";
    const wrapper = shallowMount(HelloWorld, {
      propsData: { msg }
    });
    expect(wrapper.text()).toMatch(msg);
  });
});
```

我们输入命令来运行此单元测试： 

```bash
npm run test:unit
```

这个命令会展示所有单元测试的结果。

目前，测试结果一切都ok。

我们在命令中添加--watch标志，以便在我们创建和编辑新测试时使其在后台运行。

```json
"scripts": {
  "test:unit": "vue-cli-service test:unit --watch"
}
```

## 单元测试

在这个例子中，我们将创建一个名为FancyHeading的新组件。

这是一个标题显示组件，可以使用自定义标题文案和颜色。

```vue
<template>
  <h1 :style="headingStyles">{{title}}</h1>
</template>

<script>
export default {
  data() {
    return {
      headingStyles: {
        color: this.color
      }
    };
  },
  props: ["title", "color"]
};
</script>
```

为了对此进行单元测试，我们需要在tests/unit目录中创建对应的FancyHeading.spec.js文件。

测试组件可以被认为是围绕测试特定模块或功能的测试集合。

让我们来看看Jest和Vue的第一次单元测试：

```javascript
import Vue from 'vue';
import FancyHeading from '@/components/FancyHeading.vue';

function mountComponentWithProps (Component, propsData) {
  const Constructor = Vue.extend(Component);
  const vm = new Constructor({
    propsData
  }).$mount();

  return vm.$el;
}

describe('FancyHeading.vue', () => {
  it('should be the correct color', () => {
    const headingData = mountComponentWithProps(FancyHeading, { color: 'red' });
    const styleData = headingData.style.getPropertyValue('color');

    console.log(styleData)

    expect(styleData).toEqual({ color: 'blue' });
  });

  it('should have the correct title', () => {
    const headingData = mountComponentWithProps(FancyHeading, { title: 'Hello, Vue!' });
    const titleData = headingData.textContent;

    expect(titleData).toEqual('Hello, Vue!');
  });
});
```

##  总结

1. 导入Vue和我们想要测试的组件。
2. 使用describe来封装组件的大量单元测试。
3. 每个单元测试都是使用it函数创建的，第一个参数是测试描述，第二个参数是个回调函数。
4. 在断言中，组件必选有正确的属性，我们可以封装一个方法来（例如：mountComponentWithProps）将我们的组件挂载到新的Vue实例上。
5. 使用expect来证明测试是正确的。我们用npm run test：unit --watch检查我们的结果，我们将看到这个单元测试的结果是PASS的。

>  **参考资料：** **https://alligator.io/vuejs/testing-vue-with-jest/#project-setup**

