# easychuan
在局域网内互传纯文本和文件的云剪贴板
即开即用的网页版，无须安装 APP

参考了[TransparentLC的cloud-clipboard项目](https://github.com/TransparentLC/cloud-clipboard)

`easychuan.zip 38.8M`，解压后，双击`easychuan.exe`，即可启动。

- 前端：`Ant Design Vue + Vite + Vue3`；
- 后端：`koa`

详细说明见 https://gitxieada.github.io/Demo/easychuan/index.html

## 优化
1. 前端样式兼容（Ant Design Vue）

参考：

https://www.antdv.com/docs/vue/compatible-style-cn

styleprovider无法消费样式降级 https://github.com/vueComponent/ant-design-vue/issues/6756

推荐使用hooks调用模式 https://antdv.com/components/message-cn

Vue3 + antdv4 + Vite 项目构建兼容低版本浏览器  https://juejin.cn/post/7380274613186412563

Vite配置-兼容js ：安装 @vitejs/plugin-legacy 插件和 terser 插件；

Vite配置-兼容css：安装postcss-preset-env插件。

2. 后端

修复重启服务后，id会重复的bug

修改`app\http-router.js`文件
```js
/**
 * 引入时间戳 + 36进制版本
 * 生成一个用不重复的ID
 * 函数将生成类似 ix49sfsnt7514k5wpflyb5l2vtok9y66r 的ID
 */
function GenNonDuplicateID(){
  let idStr = Date.now().toString(36)
  idStr += Math.random().toString(36).substr(2)
  return idStr
}
// 将id相关代码改为由GenNonDuplicateID生成
...
router.delete('/revoke/:id', async ctx => {
    // '/revoke/:id(\\d+)' 匹配数字
...
...

    history.receive.forEach(e => {
        if (e.type === 'file' && !uploadFileMap.has(e.cache)) return;
        messageQueue.enqueue({
            event: 'receive',
            data: {
                ...e,
                id: e.id +'', // 由于之前的id为number，为了兼容之前的旧数据，将id全部转string
            },
        });
    });
...

```
