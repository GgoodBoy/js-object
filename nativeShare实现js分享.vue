<template>
    <div class="shareStepWrap">
        <div class="share-box">
            <p class="tips">选择要分享到的平台</p>
            <div class="share">
                <div v-if="support" @click="shareTo('wechatFriend')">
                    <img :src="'../../../../static/img/course/wechat.png'" alt="">
                    <p>微信</p>
                </div>
                <div v-if="support" @click="shareTo('wechatTimeline')">
                    <img :src="'../../../../static/img/course/wechat-circle.png'" alt="">
                    <p>朋友圈</p>
                </div>
                <div @click="shareTo('weibo')">
                    <img :src="'../../../../static/img/course/weibo.png'" alt="">
                    <p>微博</p>
                </div>
                <div @click="shareTo('qqFriend')">
                    <img :src="'../../../../static/img/course/qq.png'" alt="">
                    <p>QQ</p>
                </div>
                <div class="copy-link"  ref="copyBtn" data-clipboard-action="copy" :data-clipboard-target="`#input`" @click="copy">
					<img :src="'../../../../static/img/course/copy.png'" alt=""/>
					<p>复制链接</p>
				</div>
                <input class="copy-target" id="input" :value="link" readonly/>
            </div>
            <div class="cancel" @click="onClickLeft">
                <p>取消分享</p>
            </div>
        </div> 
    </div>
</template>
<script type="text/javascript" src="./static/js/NativeShare.js"></script>
<script>
import QRCode from 'qrcodejs2'
import html2canvas from 'html2canvas';
import Clipboard from 'clipboard'
import {mapState} from 'vuex'
import siginTopBox from '../../../../static/img/user/siginShareback.png';
export default {
    data(){
        return {}
    },
    created(){
        this.nativeShare = new NativeShare();
    },
    computed:{},    
    methods:{   
        /**
		 * 浏览器是否支持微信、朋友圈分享
		 */
		browserSupport(){
			let UA = navigator.appVersion;
			let uc = UA.split('UCBrowser/').length > 1 ? 1 : 0;
			let qq = UA.split('MQQBrowser/').length > 1 ? 2 : 0;
			let wx = /micromessenger/i.test(UA);
			if(uc||qq||wx){
				this.support = true;
			}else{
				this.support = false;
			}
        },  
        /**
         * 获取模板
         */
        getShareModelList(){
            this.$http.post(this.$server.getShareModelList,{}).then(res=>{
                if(res.data.status==200){
                    if(res.data.content.length>0){
                        window.scrollTo(0,0)
                        this.posterBgd = res.data.content[0].backgroundUrl;
                    }
                }
            })
        },       
        /**
         * 分享
         */
        shareTo(command){
            var descText="我正在参加签到赢积分活动，还差一天就可以赢得大奖，现在需要你的帮助";
            var shareIcon ="https://xgxw-pic.oss-cn-beijing.aliyuncs.com/hp/120.png";
            let shareInfo = {
                title: this.nickname+'邀请您一起赢积分兑换课程',
                desc: descText,
                icon: shareIcon,
            }
			let shareData = {
                title: shareInfo.title,
                desc: descText,
                link:this.link,
                icon: shareIcon,
                success: function() {
                },
                fail:()=>{
                    this.$toast('当前浏览器不支持这类分享')
                }
            }
            this.nativeShare.setShareData(shareData)
            try {
                this.nativeShare.call(command)
            } catch(err) {

            }
        }       
    },
    mounted(){},
}
</script>
<style lang="scss" scoped>
</style>
