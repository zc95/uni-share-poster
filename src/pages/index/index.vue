<template>
    <view>
        <share-poster ref="poster">
            <!-- <image mode="widthFix" :src="tempFilePath"></image> -->
            <view>
                <text class="title">这是一段文字</text>
            </view>
        </share-poster>
        <view @click="createPoster">生成海报</view>
    </view>
</template>

<script>
import SharePoster from '@/components/share-poster/index.vue';

export default {
    components: {
        SharePoster
    },
    data() {
        return {
            imageList: [],
            tempFilePath: ''
        };
    },
    async onLoad(options) {},
    methods: {
        // 生成海报
        createPoster() {
            uni.showLoading({ title: '正在生成图片' });
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
