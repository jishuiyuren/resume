<template>
    <div id="app">
        <div id="content">
            <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
            <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
        </div>
    </div>
</template>

<script>
import StyleEditor from './components/StyleEditor'
import ResumeEditor from './components/ResumeEditor'
import './assets/reset.css'

export default {
  name: 'App',
  components: {
    StyleEditor,
    ResumeEditor
  },
  data() {
    return {
      interval: 5,
      currentStyle: '',
      enableHtml: false,
      fullStyle: [
`/*
*http://strml.net/
*大家好，我是余洁
*我来写一份简历！
*/

/*过渡效果*/
        * {
  transition: all .1s;
}
/*背景*/
html {
  color: rgb(222,222,222); background: rgb(0,64,64);
}
#content{
  height:70vh;
  margin:0;
}
#foot{
  height:29vh;
  margin:0;
}
/* 设置边框 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  overflow: auto;
  width: 50vw; height: 70vh;
  background: rgb(20,20,20);
}
/* 代码高亮 */
.token.selector{ color: rgb(130,150,0); }
.token.property{ color: rgb(190,140,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(40,160,150); }

/* 准备一个编辑器 */
.resumeEditor{
  position: fixed; right: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 50vw; height: 70vh;
  border: 1px solid;
  background: rgb(200,200,200); color: #222;
  overflow: auto;
  -webkit-transition: none;
  transition: none;
  -webkit-transform: rotateY(-10deg) translateZ(-100px) ;
          transform: rotateY(-10deg) translateZ(-100px) ;
}
/* 开始写简历 */
`,`
/*将Markdown格式翻译成HTML
 *再对HTML加点样式
*/
.resumeEditor{
  padding: 2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;
  content: counters(section, ".") " ";
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`
      ],
      currentMarkdown: '',
      currentThankMarkdown: '',
      fullMarkdown: `余洁

====
地点：上海。

前端开发工程师、一位快乐的phper、GIS开发工程师

技能
====

后端开发
----
 - 漫咖web版后端开发
 - 广告投放及管理系统
 - TV生态系统
 - 积分商城系统
 - 影视后台管理系统
 - 长链接项目
 - API接口开发

前端开发
----
 - web版漫咖开发
 - 基于Seo漫咖官方网站
 - TV生态官方网站
 - 影视官方网站
 - 积分商城管理平台
 - 支付商城管理平台
 - 智荟教育微信小程序
 - 智荟移动端开发（weex）

GIS开发
----
 -多规合一管理系统
 -一张图平台

小游戏
----
 -ibon照片列印
 -台湾化妆品活动
 -新年抽奖活动
 -用户个性签到

技术栈
----
 -JavaScript:vue, node, react, dojo
 -PHP

工作经历
----
1.上海之佐文化传媒有限公司
2.上海数慧系统技术有限公司
教育经历
----
中国矿业大学  资源与地球科学学院  理学硕士
`
    }
  },
  created() {
    this.makeResume()
  },
  methods: {
    makeResume: async function () {
      await this.progressivelyShowStyle(0);
      await this.progressivelyShowResume();
      await this.progressivelyShowStyle(1);
      await this.showHtml();
      await this.progressivelyShowStyle(2);
      await this.progressivelyShowStyle(3)
    },
    showHtml() {
      return new Promise((resolve, reject) => {
        this.enableHtml = true
        resolve()
      })
    },
    //左边代码风格
    progressivelyShowStyle(n) {
      return new Promise((resolve, reject) => {
        let interval = this.interval
        let showStyle = (async function () {
          let style = this.fullStyle[n]
          if (!style) {
            return
          }
          // 计算前 n 个 style 的字符总数
          let length = this.fullStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((p, c) => p + c, 0)
          let prefixLength = length - style.length
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - prefixLength
            let char = style.substring(l, l + 1) || ' '
            this.currentStyle += char
            if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom()
              })
            }
            setTimeout(showStyle, interval)
          } else {
            resolve()
          }
        }).bind(this)
        showStyle()
      })
    },
    progressivelyShowResume() {
      return new Promise((resolve, reject) => {
        let length = this.fullMarkdown.length
        let interval = this.interval
        let showResume = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
            let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
            let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
            if (prevChar === '\n' && this.$refs.resumeEditor) {
              this.$nextTick(() => this.$refs.resumeEditor.goBottom())
            }
            setTimeout(showResume, interval)
          } else {
            resolve()
          }
        }
        showResume()
      })
    },
  }
}
</script>

<style scoped>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
    }

    html {
        min-height: 100vh;
    }

    * {
        box-sizing: border-box;
    }
</style>
