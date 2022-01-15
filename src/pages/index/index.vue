<template>
    <view class="page">
        <share-poster ref="poster">
            <view class="poster-view">
                <view class="main-info">
                    <image class="head-img" :src="cover" mode="aspectFill" />
                    <view class="title">uni-app 分享海报、截图</view>
                    <view class="subtitle">基于renderjs、html2canvas，实现分享海报、截图。</view>
                    <view class="uni-app">UNI-APP</view>
                </view>
                <view class="desc">
                    基于renderjs、html2canvas，实现分享海报、截图。实现分享海报、截图。实现分享海报、截图实现分享海报、截图实现分享海报、截图。
                </view>
                <view class="foot">
                    <view class="left">
                        <view class="user">
                            <image class="avatar" :src="avatar" mode="aspectFill" />
                            <text class="name">张成从小就帅</text>
                        </view>
                        <view class="tip">扫码查看github</view>
                    </view>

                    <image class="code" :src="code" mode="aspectFill" />
                </view>
            </view>
        </share-poster>

        <view class="btn" hover-class="hover-class" @click="createPoster">生成海报</view>
    </view>
</template>

<script>
import SharePoster from '@/components/share-poster/index.vue';
import { pathToBase64 } from '@/components/share-poster/image-tools.js';

export default {
    components: {
        SharePoster
    },
    data() {
        return {
            cover: '', // 封面
            avatar: '', // 头像
            code: '' // 本地二维码图片
        };
    },
    async onLoad(options) {
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
        },
        // 生成海报
        createPoster() {
            uni.showLoading({ title: '正在生成海报' });
            this.$refs.poster
                .create()
                .then(res => {
                    uni.hideLoading();
                    this.previewImage(res.path);
                })
                .catch(err => {
                    console.log(err);
                });
        },
        // 预览图片
        previewImage(filePath) {
            uni.previewImage({ urls: [filePath] });
        }
    }
};
</script>

<style scoped>
.page {
    overflow: hidden;
}

.poster-view {
    font-family: BlinkMacSystemFont, 'Helvetica Neue', Arial, 'PingFang SC', 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'Microsoft JhengHei',
        'Source Han Sans SC', 'Noto Sans CJK SC', 'Source Han Sans CN', 'Noto Sans SC', 'Source Han Sans TC', 'Noto Sans CJK TC',
        'WenQuanYi Micro Hei', SimSun, sans-serif;
    overflow-x: hidden;
    text-rendering: auto;
    -webkit-font-smoothing: antialiased;
    padding: 130rpx 70rpx 70rpx 70rpx;
    box-sizing: border-box;
}

.main-info {
    background-image: linear-gradient(to bottom left, rgba(18, 67, 205, 0.07), rgba(18, 67, 205, 0));
    padding: 0 40rpx 20rpx 0;
    box-sizing: border-box;
    border-radius: 6rpx;
    position: relative;
}

.head-img {
    width: 400rpx;
    height: 400rpx;
    border-radius: 10rpx;
    margin-top: -70rpx;
}

.title {
    margin-top: 40rpx;
    font-size: 32rpx;
    font-weight: bold;
    color: #1243cd;
}

.subtitle {
    margin-top: 16rpx;
    font-size: 40rpx;
    font-weight: bold;
    color: #333333;
}

.uni-app {
    transform: rotate(90deg);
    position: absolute;
    top: 150rpx;
    right: -70rpx;
    font-size: 84rpx;
    font-weight: bold;
    color: rgba(204, 204, 204, 0.1);
}

.desc {
    margin-top: 30rpx;
    font-size: 32rpx;
    color: #111111;
    letter-spacing: 1px;
}

.foot {
    margin-top: 40rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.avatar {
    width: 50rpx;
    height: 50rpx;
    border-radius: 8rpx;
    vertical-align: middle;
}

.name {
    vertical-align: middle;
    font-weight: bold;
    margin-left: 12rpx;
    font-size: 34rpx;
}

.tip {
    margin-top: 20rpx;
    font-size: 28rpx;
    color: #909399;
}

.code {
    width: 130rpx;
    height: 130rpx;
}

.btn {
    height: 90rpx;
    line-height: 90rpx;
    text-align: center;
    margin: 40rpx;
    background-color: #1243cd;
    font-size: 32rpx;
    color: #fff;
    border-radius: 12rpx;
    font-weight: bold;
}

.hover-class {
    opacity: 0.85;
}
</style>
