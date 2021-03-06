## uni-share-poster

uni-app 分享海报、截图，基于 renderjs、html2canvas。
uni-app插件地址：[https://ext.dcloud.net.cn/plugin?id=7246](https://ext.dcloud.net.cn/plugin?id=7246)

> uni-app 中本不能实现访问、操作 dom 等行为，那要怎么去调用 html2canvas 呢？我们可以使用 renderjs，在 uni-app 中运行 for web 的 js 库，本插件基于 renderjs、html2canvas，让你更快捷的实现分享海报、截图等功能。

<img style="width: 300px;" src="https://tva1.sinaimg.cn/large/008i3skNgy1gyeeuewy9aj30n00xw0v2.jpg" />

## 先安装插件 html2canvas

> npm install --save html2canvas

## 基本用法
可以去 [https://github.com/zc95/uni-share-poster](https://github.com/zc95/uni-share-poster) clone一份demo，
文件 `src/pages/index/index.vue` 中有完整示例

```html
<share-poster ref="poster">
    <view>这里面是你的海报内容，正常拼页面就行</view>
    <view>hello world</view>
</share-poster>
```

```javascript
import SharePoster from '@/components/share-poster/index.vue';

export default {
    components: {
        SharePoster
    },
    methods: {
        // 生成海报
        createPoster() {
            this.$refs.poster
                .create()
                .then(res => {
                    // 成功后会返回一个base64图片和一个本地临时路径，{base64: '', path: ''}
                    // 因为base64在uni-app中貌似不支持预览和下载，你可以优先选择只用临时路径 path
                    console.log(res);
                })
                .catch(err => {
                    // 失败后返回错误原因，通常是图片有问题，下面会单独讲解
                    console.log(err);
                });
        }
    }
};
```

## 图片相关的问题

-   网络图片必须让服务端或者运维设置一下允许跨域（CORS），如果没人能帮你设置，你只能将其转为 base64，才能正常生成海报
-   项目中的图片，例如 `/static/code.png` ，也一样需要转为 base64，否则 html2canvas 将无法正常工作

如何批量将本地图片、网络图片转为 base64 图片：

```js
import { pathToBase64 } from '@/components/share-poster/image-tools.js';

export default {
    data() {
        return {
            cover: '', // 封面
            avatar: '', // 头像
            code: '' // 本地二维码图片
        };
    },
    async onLoad() {
        this.transfromImage();
    },
    methods: {
        // 将本地图片、网络图片 批量 转为base64图片
        transfromImage() {
            let paths = [
                'https://pic4.40017.cn/gny/line/2016/06/27/10/5kKPnc.jpg',
                'https://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTJjLbSqEKbzLoiawMOHz33vGAMLBiboibJiaKxjib8TXdkKVSfxB8eicnWCdh43gAak2GJ7ekickyOnXiagGA/132',
                '/static/code.png'
            ];
            Promise.all(paths.map(path => pathToBase64(path)))
                .then(res => {
                    [this.cover, this.avatar, this.code] = res;
                })
                .catch(error => {
                    console.error(error);
                });
        }
    }
};
```

> 最省事的就是，所有图片使用网络图片，让服务端或者运维设置一下允许跨域（CORS），这样你也不需要转化。

## 其他意外情况

比如不支持 box-shadow，还有 transform 等属性会实际有偏差，超出 n 行显示省略号有 bug，这些都是 html2canvas 这个插件比较常见的问题，你可以去网上找找方案，或者换一种思路去实现，这边就不展开讨论了。
