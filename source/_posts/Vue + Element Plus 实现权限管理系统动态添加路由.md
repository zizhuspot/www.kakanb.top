---
title: Vue + Element Plus 实现权限管理系统动态添加路由
date: 2023-9-4 23:53:54
categories:
  - 前端开发
tags:
  - VUE
  - Element plus
  - 动态添加路由
  - 前端
  - 权限管理
description: 一种利用Vue + Element Plus 实现权限管理系统动态添加路由的方法
cover: https://raw.githubusercontent.com/brvchaos/image/main/20230905001114.png
---
在开发权限系统时，根据后端返回的菜单列表动态添加路由是一个非常普遍的需求。这可以实现根据用户权限动态加载可访问的页面。在上一篇文章中，我们已经了解了如何渲染侧边栏菜单。本篇文章我们将重点介绍如何优化动态路由的添加过程。
## 将菜单列表转换为路由格式
学过 Vue 的人都知道，Vue 路由包含 name、path、component 等属性，其中 component 属性是一个函数返回一个模块，例如：

```js
{
  path: '/',
  name: 'Layout',
  component: () => import(/* webpackChunkName: "Layout" */ '../layout/index.vue'),
}
```

但是后端返回的却是一个字符串，例如 aa/bb，因此我们需要一个函数将后端返回的菜单列表处理成路由格式。在 utils 目录下新建 filterRoute.ts 文件。

```js
// 匹配 views 目录下所有的 .vue 文件
const modules = import.meta.glob('../views/**/*.vue');

export const loadView = (view: any) => {
  let res;
  for (const path in modules) {
    const dir = path.split('views/')[1].split('.vue')[0];
    if (dir === view) {
      res = () => modules[path]();
    }
  }
  return res;
};

export const filterRoute = (data: any) => {
  data.forEach((item: any) => {
    if (item.children?.length > 0) {
      delete item.component;
      filterRoute(item.children);
    } else {
      item.component = loadView(item.component);
      // item.redirect = "/404";
    }
  });
  return data;
};
```
其中，import.meta.glob('../views/**/*.vue') 可以匹配到 views 目录下所有后缀为 .vue 的文件。然后通过对比后端返回的 component 与 views/ 后面的路径来生成 Vue 路由中 component 所需要的格式。
同时，filterRoute 函数中如果菜单是父菜单，则其不能有 component 属性。
根据以上规则可以看出，如果菜单 component 配置了 aa/bb 则我们需要在 views 目录下创建 aa/bb.vue 文件才能匹配当前组件路径。
## 引入 Pinia

```css
npm i pinia
```
然后在 main.ts 这个文件里注册：

```js
import { createPinia } from 'pinia';
const app = createApp(App);

const pinia = createPinia();
app.use(pinia).use(router).mount('#app');
```
新建 store/index  用于存放 Pinia 管理的状态，同时这里我们定义了获取菜单的方法 GenerateRoutes：

```js
import { defineStore } from 'pinia';
import { getMenuList } from '@/http/menu/index';
import router from '@/router';
import { MenuVo } from '@/http/menu/types/menu.vo';

type StoreState = {
  isCollapse: boolean;
  menuList: MenuVo[];
};

export default defineStore('home', {
  state: (): StoreState => {
    return {
      isCollapse: false,
      menuList: [],
    };
  },
  actions: {
    async GenerateRoutes() {
      const { data } = await getMenuList({});
      this.menuList = data;
      return data;
    },
  },
});
```

然后在路由守卫 beforeEach 中使用 addRoute 进行动态路由添加，同时定义一个白名单路由列表，这里暂时只有一个 login 页面，表示直接放行：
```js
import home from '@/store';
import { nextTick } from 'vue';
import { filterRoute } from '@/utils/filterRoute';

...

const whiteLists = ['login'];

router.beforeEach(async (to, from, next) => {
  if (whiteLists.includes(to.name as string)) {
    next();
    return;
  }

  await nextTick();
  const homeStore = home();
  if (homeStore.menuList.length) {
    next();
    return;
  }

  const data = await homeStore.GenerateRoutes();
  const routers = filterRoute(data);
  routers.forEach((route: RouteRecordRaw) => {
    router.addRoute('Layout', route);
  });

  next({ ...to, replace: true });
});
```

## PS:
这里在获取 pinia 中数据时,先执行了 await nextTick(); 是因为此时如果直接拿 pinia 中数据是拿不到的,因为 pinia 还没有初始化完毕 next({ ...to, replace: true }) 再次加载当前路由,即重新执行 beforeEach ,
因为第一次进入 beforeEach 函数中的时候还没有加载动态路由,所以我们需要在添加完路由后重新加载当前路由以触发 beforeEach 钩子函数.
## 路由跳转
我们模拟修改了菜单表中子菜单 2.1 的 component 为 test2/index。
![](https://raw.githubusercontent.com/brvchaos/image/main/20230905003731.png)
相应地，我们需要在 views 目录下新建 test2/index.vue 文件

```js
<template>
  <div>test2</div>
</template>

<script lang="ts" setup></script>
```
接下来，我们需要在 layout/components/sidebar.vue 文件中处理菜单选择事件。
![](https://raw.githubusercontent.com/brvchaos/image/main/20230905003903.png)
其中，getPath 函数中的 d 参数代表当前菜单的父菜单和子菜单的索引数组，即父菜单的 path 和子菜单的 path，例如 ['aa', 'bb']，我们可以根据它们进行相应的路由跳转。
最后，当我们点击子菜单 2.1 时，就会发现这个页面被渲染出来了。
![](https://raw.githubusercontent.com/brvchaos/image/main/20230905004003.png)

## 写在后面
到这里动态路由添加基本完成了。当然，可能还会有一些细枝末节需要处理。




