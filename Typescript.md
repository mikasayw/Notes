## :black_nib: Typescrpt

- #### :maple_leaf: Vue中的ref类型

  ```javascript
  // 1.instanceType作用:该类型的作用是获取构造函数类型的实例类型;
  // 2.vue中的instanceType: 父组件用ref获取子组件时，通过 instanceType获取子组件的类型;

  const component = ref<InstanceType<typeof component> | null>(null)

  component.value?.feature()
  ```
          
