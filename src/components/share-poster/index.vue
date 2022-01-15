<template>
    <view id="sharePosterId" :timestamp="timestamp" :change:timestamp="renderScript.generateImage">
        <slot></slot>
    </view>
</template>

<script>
import { base64ToPath } from './image-tools.js';

export default {
    data() {
        return {
            timestamp: 0, // 时间戳，监听到该值变化时，可以触发randerjs内的方法
            callbackInfo: null, // renderjs返回的数据
            timer: null // 定时器
        };
    },
    methods: {
        create() {
            clearInterval(this.timer);
            this.callbackInfo = null;
            this.timestamp = new Date().getTime();

            return new Promise((resolve, reject) => {
                this.timer = setInterval(async () => {
                    if (!this.callbackInfo) return;
                    clearInterval(this.timer);
                    let { code, base64, err } = this.callbackInfo;
                    if (code === 1) {
                        let path = await base64ToPath(base64);
                        resolve({ base64, path });
                    } else {
                        uni.hideLoading();
                        reject(new Error(err));
                    }
                }, 100);
            });
        },
        callback(res) {
            this.callbackInfo = res;
        }
    }
};
</script>

<script lang="renderjs" module="renderScript">
import html2canvas from 'html2canvas';

export default {
    methods: {
        generateImage(newValue, oldValue, ownerInstance, instance) {
            const dom = document.getElementById('sharePosterId');
                html2canvas(dom, {
                width: dom.clientWidth, //dom 原始宽度
                height: dom.clientHeight,
                scrollY: 0, // html2canvas默认绘制视图内的页面，需要把scrollY，scrollX设置为0
                scrollX: 0,
                useCORS: true //支持跨域
                // scale: 2, // 设置生成图片的像素比例，默认是1，如果生成的图片模糊的话可以开启该配置项
            })
                .then(canvas => {
                    const base64 = canvas.toDataURL('image/png')?.replace(/[\r\n]/g, '');
                    ownerInstance.callMethod('callback', {
                        code: 1,
                        base64
                    });
                })
                .catch(err => {
                    err = err.message
                    ownerInstance.callMethod('callback', {
                        code: 0,
                        err
                    });
                });
        }
    }
};
</script>
