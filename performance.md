## :black_nib: 性能优化实践

### 网络层面
- #### :maple_leaf: DNS预解析
  ##### dns-prefetch 仅对跨域域上的 DNS查找有效，因此请避免使用它来指向您的站点或域。因为到浏览器看到提示时，您站点域背后的 IP 已经被解析。
  ```html
  <meta http-equiv="x-dns-prefetch-control" content="on">
  <link rel="dns-prefetch" href="https://fonts.googleapis.com/">
  ```
- #### :maple_leaf: Http cache
  - #####  强缓存 
    #####  强缓存不会向服务器发送请求，而是直接从缓存中读取资源。返回code是200。 
    #####   * HTTP1.0和HTTP1.1 Header 实现：Expires 和 Cache-Control。
  - #####  协商缓存 
    #####  如果没有命中强缓存，浏览器会发送请求至服务器，通过last-modified和Etag验证资验证资源是否命中协商缓存，如果命中，服务器会将这个请求返回，但不返回这个资源的数据，而是从缓存中读取资源。返回code是304。
    ##### * 单页面打包后会生成一个index.html，和一堆 *.[hash].js 和 *.[hash].css 文件等。我们可以将 index.html 的 Cache-Control 设置为 no-cache，这样每次客户端请求 index.html可以和服务器进行协商缓存。同时将其它资源设置为强制缓存。
- #### :maple_leaf: CDN
  ##### CDN会把源站的资源缓存到CDN服务器，当用户访问的时候就会从最近的CDN服务器拿取资源而不是从源站拿取，分散了服务器压力，提升返回访问速度。

### 构建层面

- 解析优化
  * include/exclude，减少不必要的检索解析
  * caChe 大部分Loader/Plugin都会提供一个可使用编译缓存的选项
  * alias 映射模块路径
  * extensions: [".js", ".ts", ".jsx", ".tsx", ".json", ".vue"] // import路径时文件可省略后缀名
  * DLL 动态链接库，在4.x已不推荐，版本迭代带来的性能可以忽略DllPlugin，配置复杂
  * 配置Thread将Loader单进程转换为多进程
  * BundleAnalyzer 依赖体积分析
  * 4.x之后splitChunks（内置）代替CommonsChunksPlugin，
  * runtime 抽出内联
  * treeshaking webpack和vite不一样
