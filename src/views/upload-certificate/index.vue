<template>
    <div class="page">
        <div class="page-header">
            <van-nav-bar left-arrow placeholder fixed :title="visaTitle" :border="false" @click-left="onBack" />
        </div>

        <div class="page-body" style="padding-bottom: 130px;">
            <template v-for="(item, index) in uploadList">
                <div class="file-box flex space-between border-bottom1" v-if="item.required == 1">
                    <div class="file-box-label flex">
                        <span class="font14"><i style="color: red" v-if="item.type != 1583">*</i> {{ item.title }}</span>
                        <div class="font14 mg-l16 " style="color: rgb(4, 134, 254);">
                            <div class="flex align-middle">
                                <van-icon size="20px" name="question-o" @click="onViewTips(item)" />
                                <span v-if="index === 0">查看范例</span>
                                <!-- <a v-if="item.type == 1709 && isIOS" href="#" @click="downloadPdf(wordUrl,'不随行父母同意函模板')">下载</a>
                                <a v-if="item.type == 1709 && !isIOS" :href="wordUrl" download="不随行父母同意函模板.docx">下载</a> -->
                                <!-- <a v-if="item.type == 1710 && isIOS" href="#" @click="downloadPdf(studyWordUrl,'在读证明模板')">下载</a>
                                <a v-if="item.type == 1710 && !isIOS" :href="studyWordUrl" download="在读证明模板.docx">下载</a> -->
                                <a v-if="item.type == 1709 " :href="wordUrl" download="不随行父母同意函模板.docx">下载</a>
                                <a v-if="item.type == 1710 " :href="studyWordUrl" download="在读证明模板.docx">下载</a>
                            </div>
                        </div>
                    </div>
                    <div class="flex flex-column">
                        <span @click="toViewPdf(item.fileUrl, item.fileName)" class="font14 mg-b10"
                            style="color: rgb(1, 145, 255);">{{
                                item.fileName }}</span>
                        <div class="text-right">
                            <van-uploader :readonly="item.status === 1" :before-read="(e) => beforeRead(e, item.type)"
                                :after-read="(items, detail) => afterRead(items, detail, item)" accept=".pdf">
                                <van-button style="height: 30px;width: 85px;" :color="buttonColor(item.status)">
                                    <span class="font14">{{ buttonText(item.status) }}</span>
                                </van-button>
                            </van-uploader>
                        </div>
                    </div>
                    
                </div>
            </template>

        </div>

        <div class="page-footer pd24">
            <van-button block type="primary" loading-type="circular" @click="onBack">
                完成
            </van-button>
        </div>

        <van-popup v-model:show="show" round closeable close-icon-position="top-left" position="bottom"
            :style="{ height: '80%' }">
            <div class="tips">
                <div class="tips-header flex justify-center  border-bottom1">
                    <h3 class="tips-header-title">{{ activeItem?.title }}</h3>
                </div>
                <div class="tips-body pd20">
                    <div class="txt">
                        <div v-show="typeMsg == 1530">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/huzhao_1.jpg" alt="护照首页">
                            </div>
                            <div class="txt_bottom">
                            <h4>护照首页彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）护照有效期需2025年11月15日之后</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1531">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/qianzheng.png" alt="签证页">
                            </div>
                            <div class="txt_bottom">
                            <h4>签证页彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）签证页有效期需在2024年12月15日之后</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1581">
                            <div class="img_top">
                            <img src="@/assets/image/huzhao_1.jpg" alt="护照首页">
                            <img src="@/assets/image/huzhao_2.jpg" alt="护照内容页">
                            <img src="@/assets/image/huzhao_3.jpg" alt="护照内容页">
                            </div>
                            <div class="txt_bottom">
                            <h4>护照整本彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）需包含护照所有信息页、备注页、签证页、盖章页以及签名页（空白页无需扫描）；</p>
                            <p>2）护照有效期需至行程结束后6个月，即2025年11月15日之后；</p>
                            <p>3）护照至少需保留两页空白签证页（不含备注页），新版电子护照无需签名；</p>
                            <p>4）所有页面需扫描保存为一个PDF文档，且页面必须清晰完整，不歪斜，无杂物（不可以拍照）。</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1583">
                            <div class="img_top">
                            <img src="@/assets/image/huzhao_1.jpg" alt="护照首页">
                            <img src="@/assets/image/huzhao_2.jpg" alt="护照内容页">
                            <img src="@/assets/image/huzhao_3.jpg" alt="护照内容页">
                            </div>
                            <div class="txt_bottom">
                            <h4>旧护照彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）如有旧护照请一并提供，需包含护照所有信息页、备注页、签证页、盖章页以及签名页（空白页无需扫描）</p>
                            <p>2）所有页面需扫描保存为一个PDF文档，且页面必须清晰完整，不歪斜，无杂物（不可以拍照）</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1505">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/id_card.jpg" alt="彩色身份证">
                            </div>
                            <div class="txt_bottom">
                            <h4>身份证正反面彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>要求：正反两面需扫描在同一页A4纸内。</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1588">
                            <div class="img_top">
                            <img src="@/assets/image/hukouben1.jpg" alt="户口本户主首页">
                            <img src="@/assets/image/hukouben2.jpg" alt="户口本户主信息">
                            <img src="@/assets/image/hukouben3.jpg" alt="户口本家庭成员">
                            </div>
                            <div class="txt_bottom">
                            <h4>户口本整本彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）需包含户主首页、户主信息页及所有家庭成员页（空白页无需扫描）；</p>
                            <p>2）如是集体户口，需提交户主首页及本人信息页扫描件。</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1591">
                            <div class="img_top img_only">
                            <img src="@/assets/image/money.jpg" alt="财力证明">
                            </div>
                            <div class="txt_bottom">
                            <h4>财力证明彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>要求：本人名下近6个月银行流水对账单扫描件，需加盖银行公章，能够体现个人真实收入来源，有多笔进出往来账目明细，余额需大于3万，建议不要近期一次性存入。</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1580">
                            <div class="img_top img_two">
                            <img src="@/assets/image/jiehun_card.jpg" alt="结婚证">
                            <img src="@/assets/image/chusheng_card.jpg" alt="出生证明">
                            </div>
                            <div class="txt_bottom">
                            <h4>关系证明彩色扫描件--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）如携带家属与本人持同一户口本，则可无需额外提供其它关系证明；</p>
                            <p>2）如携带家属与本人持不同户口本，则需提供其它关系证明（夫妻关系可提供结婚证彩色扫描件；父母或子女关系可提供出生证明彩色扫描件或公安派出所出具相关证明的彩色扫描件）；</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1705">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/baoxian.jpg" alt="保险单">
                            </div>
                            <div class="txt_bottom">
                            <h4>75周岁以上老人需额外提供材料--</h4>
                                <p>1）递交签证申请后，待领馆通知，需前往领馆指定医院进行体检；
                                    点击链接可查看指定医院名单：<a href="https://china.embassy.gov.au/bjngchinese/DIMAcn42.html" target="_blank">https://china.embassy.gov.au/bjngchinese/DIMAcn42.html</a>
                                </p>
                                <div>2）医疗保险（<span class="span_red">PDF文档</span>，保险需适用于澳大利亚目的地并覆盖全部行程日期，且境外意外医疗保额需在人民币50万元以上）。</div>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1706">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/id_card.jpg" alt="父母身份证">
                            <!-- <img src="@/assets/image/jiehun_card.jpg" alt="结婚证">
                            <img src="@/assets/image/chusheng_card.jpg" alt="出生证明"> -->
                            </div>
                            <div class="txt_bottom">
                            <h4>18周岁以下未成年人需额外提供材料--<span class="span_red">PDF文档</span>：</h4>
                            <!-- <p>1）父母结婚证或离婚证彩色扫描件（PDF文档）；</p> -->
                            <p>1）父母双方身份证彩色扫描件（PDF文档，正反两面需扫描在同一页A4纸上）；</p>
                            <!-- <p>3）出生证明彩色扫描件（PDF文档，出生证明需清晰显示父母及小朋友姓名）；</p>
                            <p>4）父母其中一方不出席，需填写“不随行父母同意书”，请下载并签署。</p> -->
                            </div>
                        </div>
                        <div v-show="typeMsg == 1707">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/lihun_card.jpg" alt="结婚证或离婚证">
                            </div>
                            <div class="txt_bottom">
                            <h4>18周岁以下未成年人需额外提供材料--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）父母结婚证或离婚证彩色扫描件（PDF文档）；</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1708">
                            <div class="img_top" style="justify-content:center">
                            <img src="@/assets/image/chusheng_card.jpg" alt="出生证明">
                            </div>
                            <div class="txt_bottom">
                            <h4>18周岁以下未成年需额外提供材料--<span class="span_red">PDF文档</span>：</h4>
                            <p>1）出生证明彩色扫描件（PDF文档，出生证明需清晰显示父母及小朋友姓名）；</p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1709">
                            <div class="img_top" style="justify-content: center">
                            <img src="@/assets/image/bsx.jpg" alt="不随行父母同意函">
                            </div>
                            <div class="txt_bottom">
                                <h4>不随行父母同意函--<span class="span_red">PDF文档</span>：</h4>
                                <p >1）父母其中一方不出席，需填写“不随行父母同意函”，请 
                                    <!-- <a v-if="isIOS" href="#" @click="downloadPdf(wordUrl,'不随行父母同意函模板')" class="down_style">下载</a> 
                                    <a v-else :href="wordUrl" class="down_style" download="不随行父母同意函模板.docx">下载</a>并签署.  -->
                                    <a :href="wordUrl" class="down_style" download="不随行父母同意函模板.docx">下载</a>并签署. 
                                </p>
                            </div>
                        </div>
                        <div v-show="typeMsg == 1710">
                            <div class="img_top" style="justify-content: center">
                            <img src="@/assets/image/zaidu.jpg" alt="在读证明模版">
                            </div>
                            <div class="txt_bottom">
                                <h4>在读证明彩色扫描件----<span class="span_red">PDF文档</span>（<span class="span_red">仅限在校学生额外提供</span>）：</h4>
                                <p >1）请
                                    <!-- <a v-if="isIOS" href="#" @click="downloadPdf(studyWordUrl,'在读证明模版')" class="down_style">下载</a>  -->
                                    <a :href="studyWordUrl" class="down_style" download="在读证明模版.docx">下载</a>在读证明模版并按要求提供，需使用学校抬头纸打印，抬头纸包括学校名称、地址、电话和传真；
                                </p>
                                <p>2）签名处为班主任或其他老师亲笔签名，并加盖学校公章，电话为签字人电话需可以联系到。</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </van-popup>
    </div>
</template>

<script lang="ts" setup>
import { ref, computed, onMounted } from "vue";
import { useRouter, useRoute } from "vue-router";
import { useToggle } from '@vant/use';
import { showLoadingToast, showToast } from "vant";
import { UploaderFileListItem } from "vant/lib/uploader/types";
import { Numeric } from "vant/lib/utils";
import { getVisa, uploadFile } from '@/api/user'

export interface UploadList {
    title: string;
    required: number,
    fileUrl?: string,
    fileName?: string,
    status: number,
    type: number
}

const router = useRouter();
const route = useRoute();

const [show, showToggle] = useToggle(false);
let activeItem = ref<UploadList>();

const uploadList = ref<UploadList[]>([])

const buttonColor = computed(() => {
    return (status: number): string => {
        let color = '#2678FF';
        switch (status) {
            case 1:
                color = '#999999';
                break;
            default:
                break;
        }
        return color
    }
})


const buttonText = computed(() => {
    return (status: number): string => {
        let text = '上传';
        switch (status) {
            case 1:
                text = '已审批';
                break;
            default:
                break;
        }
        return text
    }
})
let visaTitle = ref()
// 获取签证信息
const getList = () => {
    let id = Number(route.params.id)
    getVisa(id).then(res => {
        if (res.code == 0) {
            uploadList.value = res.data.materials
            visaTitle.value = res.data.name
        }
    })
}
// 测试
// let wordUrl = window.location.origin+'/Amway2025MEL/doc/不随行父母同意函.docx'
// let studyWordUrl = window.location.origin+'/Amway2025MEL/doc/在读证明模版.docx'
// 正式
let wordUrl = window.location.origin+'/doc/不随行父母同意函.docx'
let studyWordUrl = window.location.origin+'/doc/在读证明模版.docx'
// const downloadPdf = (url:string, tagFileName:string) => {
//         const xhr = new XMLHttpRequest();
//         xhr.open('GET', url, true);
//         xhr.responseType = 'blob';
//         xhr.onload = () => {
//           if (xhr.status === 200) {
//             const blob = new Blob([xhr.response], { type: 'application/msword' });
//             const link = document.createElement('a');
//             link.href = window.URL.createObjectURL(blob);
//             link.download = tagFileName+'.docx'; // 下载的文件名
//             link.click();
//           }
//         };
//         xhr.send();
//     }
let isIOS = ref(true)
onMounted(() => {
    getList()
    let u = navigator.userAgent;
    isIOS.value = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);   //判断是否是 iOS终端
})

// 拦截
const beforeRead = (file: File | File[], type: number) => {
    const _file = file as File;
    console.log(file);
    const canUpType: string[] = ['application/pdf'];

    if (!canUpType.includes(_file.type)) {
        showToast('请上传pdf格式文件');
        return false;
    }
    const toast1 = showLoadingToast({
        message: '上传中...',
        forbidClick: true,
        duration: 20000
        });
    let fd = new FormData()
    fd.append('fileData', file as any)
    fd.append('trID', route.params.id as string)
    fd.append('type', type as any)
    uploadFile(fd).then(res => {
        if (res.code == 0) {
            getList()
        }
    }).finally(() => {
        toast1.close();
    })
    return false;
};

type AfterRead = (items: UploaderFileListItem | UploaderFileListItem[], detail: {
    name: Numeric;
    index: number;
}, upItem: UploadList) => void


const afterRead: AfterRead = (items, detail, upItem) => {
    console.log(items, detail, upItem);
    const loadingToast = showLoadingToast({
        message: '上传中',
        forbidClick: true
    })
    try {
        // upItem.fileName = (items as UploaderFileListItem).file?.name;
    } catch (error) {

    } finally {
        setTimeout(() => {
            loadingToast.close();
            upItem.fileName = (items as UploaderFileListItem).file?.name;
            upItem.fileUrl = (items as UploaderFileListItem).objectUrl;
            showToast('上传成功 ');
        }, 1000)
    }
}


const onBack = () => router.back();
let typeMsg = ref(0)
const onViewTips = (item: UploadList) => {
    activeItem.value = item;
    console.log(item,'item');
    typeMsg.value = item.type
    showToggle(true)
}

const toViewPdf = (url: UploadList['fileUrl'], name: UploadList['fileName']): void => {
    console.log("🚀 ~ toViewPdf ~ url:", url)
    if (!url) return;
    // router.push({
    //     name: "pdfView",
    //     // params: {
    //     //     // pdf_url: url,
    //     //     pdf_name: name
    //     // },
    //     query: {
    //         pdf_name: name,
    //         file: window.btoa(url)
    //     }
    // })
    console.log(window.location, name);

    window.location.href = window.origin + import.meta.env.VITE_BASE_URL + '/pdfJS/web/viewer.html?file=' + url;
}

</script>

<style scoped lang="less">
.file-box {
    height: 80px;
    width: 100%;
    margin-bottom: 24px;
}

.tips {
    height: 100%;

    &-header {
        // height: 74px;
        // line-height: 74px;
        padding: var(--van-popup-close-icon-margin);
        width: 100%;

        &-title {
            font-size: 16px;
            font-weight: bold;
        }
    }

    &-body {
        overflow: auto;
        height: calc(100% - 54px);
    }
}
.img_top {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    img {
      width: 250px;
      margin-bottom: 10px;
      object-fit: cover;
    }
  }
  .txt_bottom {
    h4 {
      color: #333;
    }
    span {
      color: #f08d56;
    }
    .down_style {
      color: #0008ff;
      cursor: pointer;
    }
    p {
        margin-top: 4px;
    }
  }
  .span_red {
    color: #ef2d2d !important;
  }
</style>