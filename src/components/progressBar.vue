<template>
    <transition name="fade">
        <el-progress class="progress-bar" :show-text="false" :percentage="10"></el-progress>
    </transition>
</template>
<script>
export default {
    data() {
        return {
            isShow: true, // 是否显示进度条
            val: 0, // 进度
        }
    },
    props: {
        /**
         * 每10毫秒自增幅度
         */
        step: {
            type: Number,
            default: 5,
        },
        /**
         * 初始值
         */
        initVal: {
            type: Number,
            default: 0,
        },
        /**
         * 到一定进度停止
         */
        stopVal: {
            type: Number,
            default: 80,
        },
        /**
         * 进度条继续到成功
         */
        isOk: {
            type: Boolean,
            default: false,
        },
    },
    mounted() {
        // 初始化后加载进度，加载到百分之多少由stopVal决定
        this.val = this.initVal
        let step = this.step
        let timer = setInterval(() => {
            this.val = this.val + step
            this.$el.style.width = this.val + '%'
            // 父组件数据加载完前进度条最多到stopVal的这个百分值
            if (this.val >= this.stopVal) {
                clearInterval(timer)
                return
            }
        }, 10)
    },
    watch: {
        /**
         * 监听组件props变化决定是否继续加载，一般在父组件数据加载完后改变此标志位
         */
        isOk() {
            let val = this.val
            let step = this.step
            let timer = setInterval(() => {
                val = val + step
                this.$el.style.width = val + '%'
                // 加载到百分百完成
                if (val >= 100) {
                    // 关闭定时器
                    clearInterval(timer)
                    // 加载完成关闭进度条
                    this.isShow = false
                    // 加载完成的回调
                    this.$emit('callback', 'load success')
                    return
                }
            }, 10)
        },
    },
}
</script>
<style lang="less" scoped>
.progress-bar {
    position:fixed;
    top:0;
    height: 6px ;
    width: 100vw!important;
    width: 0 ;
    z-index: 10000;
    background-color: #000;
}

.fade {
    .enter-active,
    .leave-active {
        transition: all .3s
    }
    .enter,
    .leave-active {
        opacity: 0
    }
}
</style>