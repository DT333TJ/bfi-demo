<template>
  <div class="dashboard-container">
    <h2><i class="el-icon-office-building" style="margin-right: 10px" />基础介绍</h2>
    <p :style="colorObj">
      <b>由于更换主题需要外网资源支持，本展示功能仅在外网环境预览</b>
    </p>

    <div class="dashboard-item">
      <span class="dashboard-item-label">主题：</span>
      <el-color-picker
        v-model="theme"
        :predefine="['#409EFF', '#1890ff', '#304156','#212121','#11a983', '#13c2c2', '#6959CD', '#f5222d', ]"
        size="small"
        class="dashboard-item-label"
      />
      <span :style="colorObj" class="dashboard-item-label">当前值:{{ defaultTheme }}</span>
    </div>

    <div class="dashboard-item">
      <span class="dashboard-item-label">内网变换主题(换肤)：</span>
      <div class="dashboard-item-label dashboard-item-special-label">
        <el-input
          v-model="textsObj.themeText"
          type="textarea"
          :readonly="true"
          :rows="2"
        />
      </div>
      <p>相关链接：<a href="https://segmentfault.com/a/1190000009762198#articleHeader2" target="_blank" style="text-decoration:underline">手摸手，带你用vue撸后台 系列三(实战篇）</a></p>
    </div>

    <div class="dashboard-item">
      <span class="dashboard-item-label">gulp相关代码：</span>
      <pre class="dashboard-item-label">
        <code>
          var path = require('path')
          var gulp = require('gulp')
          var cleanCSS = require('gulp-clean-css');
          var cssWrap = require('gulp-css-wrap');
          var customThemeName='.custom-theme'
          gulp.task('css-wrap', function() {
            return gulp.src( path.resolve('./theme/index.css'))
              .pipe(cssWrap({selector:customThemeName}))
              .pipe(cleanCSS())
              .pipe(gulp.dest('dist'));
          });
          gulp.task('move-font', function() {
            return gulp.src(['./theme/fonts/**']).pipe(gulp.dest('dist/fonts'));
          });
          gulp.task('default',['css-wrap','move-font']);
        </code>
      </pre>
    </div>

    <div class="dashboard-item">
      <p>当前主题各类颜色：</p>
      <el-row>
        <el-col v-for="item in colors" :key="item.key" :span="3" style="padding: 0 4px">
          <div class="dashboard-item-box" :style="{background: item.value}">
            <span class="dashboard-item-box-label">{{ item.key }}</span>
            <span class="dashboard-item-box-label">{{ item.value }}</span>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
const version = require('element-ui/package.json').version // element-ui version from node_modules
const ORIGINAL_THEME = '#409EFF' // default color, can't change
import variables from '@/styles/element-variables.scss'
import textObj from '@/textConfig'
import { color } from '@/utils/index'

import { mapState } from 'vuex'

export default {
  name: 'Dashboard',
  data() {
    return {
      chalk: '', // content of theme-chalk css
      theme: '',
      colors: []
    }
  },
  computed: {
    ...mapState({
      defaultTheme: state => state.settings.theme,
      name: state => state.user.name
    }),
    colorObj() {
      return {
        color: this.defaultTheme
      }
    },
    textsObj() {
      return textObj
    }
  },
  watch: {
    defaultTheme: {
      handler: function(val, oldVal) {
        this.theme = val
      },
      immediate: true
    },
    async theme(val) {
      const oldVal = this.chalk ? this.theme : ORIGINAL_THEME
      if (typeof val !== 'string') return
      const themeCluster = this.getThemeCluster(val.replace('#', ''))
      const originalCluster = this.getThemeCluster(oldVal.replace('#', ''))
      console.log(themeCluster, originalCluster)
      const getHandler = (variable, id) => {
        return () => {
          const originalCluster = this.getThemeCluster(ORIGINAL_THEME.replace('#', ''))
          const newStyle = this.updateStyle(this[variable], originalCluster, themeCluster)
          let styleTag = document.getElementById(id)
          if (!styleTag) {
            styleTag = document.createElement('style')
            styleTag.setAttribute('id', id)
            document.head.appendChild(styleTag)
          }
          styleTag.innerText = newStyle
        }
      }
      if (!this.chalk) {
        const url = `https://unpkg.com/element-ui@${version}/lib/theme-chalk/index.css`
        await this.getCSSString(url, 'chalk')
      }
      const chalkHandler = getHandler('chalk', 'chalk-style')
      chalkHandler()
      const styles = [].slice.call(document.querySelectorAll('style'))
        .filter(style => {
          const text = style.innerText
          return new RegExp(oldVal, 'i').test(text) && !/Chalk Variables/.test(text)
        })
      styles.forEach(style => {
        const { innerText } = style
        if (typeof innerText !== 'string') return
        style.innerText = this.updateStyle(innerText, originalCluster, themeCluster)
      })
      this.$store.dispatch('settings/changeSetting', {
        key: 'theme',
        value: val
      })
    }
  },
  mounted() {
    this.colors = color(variables)
    console.log('variables', color(variables))
  },
  methods: {
    updateStyle(style, oldCluster, newCluster) {
      let newStyle = style
      oldCluster.forEach((color, index) => {
        newStyle = newStyle.replace(new RegExp(color, 'ig'), newCluster[index])
      })
      return newStyle
    },
    getCSSString(url, variable) {
      return new Promise(resolve => {
        const xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
          if (xhr.readyState === 4 && xhr.status === 200) {
            this[variable] = xhr.responseText.replace(/@font-face{[^}]+}/, '')
            resolve()
          }
        }
        xhr.open('GET', url)
        xhr.send()
      })
    },
    getThemeCluster(theme) {
      const tintColor = (color, tint) => {
        let red = parseInt(color.slice(0, 2), 16)
        let green = parseInt(color.slice(2, 4), 16)
        let blue = parseInt(color.slice(4, 6), 16)
        if (tint === 0) { // when primary color is in its rgb space
          return [red, green, blue].join(',')
        } else {
          red += Math.round(tint * (255 - red))
          green += Math.round(tint * (255 - green))
          blue += Math.round(tint * (255 - blue))
          red = red.toString(16)
          green = green.toString(16)
          blue = blue.toString(16)
          return `#${red}${green}${blue}`
        }
      }
      const shadeColor = (color, shade) => {
        let red = parseInt(color.slice(0, 2), 16)
        let green = parseInt(color.slice(2, 4), 16)
        let blue = parseInt(color.slice(4, 6), 16)
        red = Math.round((1 - shade) * red)
        green = Math.round((1 - shade) * green)
        blue = Math.round((1 - shade) * blue)
        red = red.toString(16)
        green = green.toString(16)
        blue = blue.toString(16)
        return `#${red}${green}${blue}`
      }
      const clusters = [theme]
      for (let i = 0; i <= 9; i++) {
        clusters.push(tintColor(theme, Number((i / 10).toFixed(2))))
      }
      clusters.push(shadeColor(theme, 0.1))
      return clusters
    }
  }
}
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px
  }
  &-item {
    width: 100%;
    margin-bottom: 20px
  }
  &-item-label {
    display: inline-block;
    margin-right: 10px;
    vertical-align: middle;
  }
  &-item-special-label {
    width: calc(100% - 180px)
  }

  &-item-box {
    box-sizing: border-box;
    border-radius: 4px;
    padding: 20px;
    height: 80px;
    font-size: 14px;
    color: white
  }
  &-item-box-label {
    display: block;
    text-align: center;
  }
}
</style>
