<template>
  <div class="dashboard-container">
    <h2><i class="el-icon-office-building" style="margin-right: 10px" />element-ui@2.13.0版本框架基础介绍</h2>

    <div class="dashboard-item">
      <h4>默认theme-chalk主题各类颜色：</h4>
      <el-row>
        <el-col v-for="item in colors" :key="item.key" :span="3" style="padding: 0 4px">
          <div class="dashboard-item-box" :style="{background: item.value}">
            <span class="dashboard-item-box-label">{{ item.key }}</span>
            <span class="dashboard-item-box-label">{{ item.value }}</span>
          </div>
        </el-col>
      </el-row>
    </div>

    <div class="dashboard-item">
      <h4>按钮：</h4>
      <el-row class="dashboard-btn-box">
        <el-button>默认按钮</el-button>
        <el-button plain>plain</el-button>
        <el-button disabled>disabled</el-button>
        <el-button type="primary" :loading="true">加载中</el-button>
        <el-button type="success" round>round</el-button>
        <el-button type="warning" size="medium">size:medium</el-button>
        <el-button type="danger" size="small">size:small</el-button>
        <el-button type="info" icon="el-icon-message" size="mini" circle title="图标按钮" />
      </el-row>
    </div>

    <div class="dashboard-item">
      <h4>表格、分页、dialog、loading：</h4>
      <el-table
        ref="multipleTable"
        v-loading="loading"
        :data="tableData"
        tooltip-effect="dark"
        style="width: 100%"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="55" />
        <el-table-column label="日期">
          <template slot-scope="scope">{{ scope.row.date }}</template>
        </el-table-column>
        <el-table-column prop="name" label="姓名" />
        <el-table-column prop="address" label="地址" width="180" show-overflow-tooltip />
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)"
            >编辑弹窗</el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
            >删除</el-button>
            <el-button
              size="mini"
              type="primary"
              @click="openLoading"
            >loading</el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination
        :page-size="30"
        :pager-count="13"
        layout="prev, pager, next"
        prev-text="前一页"
        next-text="后一页"
        :total="100"
        style="text-align:center;margin-top: 20px"
      />

      <el-dialog
        title="提示"
        :visible.sync="dialogVisible"
        width="30%"
      >
        <span>这是一段信息</span>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
      </el-dialog>
    </div>

    <div class="dashboard-item">
      <h4>表单、提示：</h4>
      <el-row>
        <el-col :span="6" style="margin-bottom: 10px">
          <label class="dashboard-item-form-label" style="width: 60px">input</label>
          <div style="margin-left: 60px">
            <el-input v-model="input" placeholder="请输入内容" />
          </div>
        </el-col>

        <el-col :span="18" style="margin-bottom: 10px">
          <label class="dashboard-item-form-label" style="width: 60px">select</label>
          <span class="dashboard-item-form-span" style="width: 500px">默认宽度为220px,修改.el-select的width: 100%属性,保证宽度的百分百</span>
          <div style="margin-left: 60px;margin-right: 510px">
            <el-select v-model="select" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </div>
        </el-col>

        <el-col :span="8" style="margin-bottom: 10px">
          <label class="dashboard-item-form-label" style="width: 210px">date(默认宽度为220px)</label>
          <div style="margin-left: 210px">
            <el-date-picker
              v-model="date"
              style="width: 100%"
              type="date"
              placeholder="选择日期"
            />
          </div>
        </el-col>

        <el-col :span="24" style="margin-bottom: 10px">
          <label class="dashboard-item-form-label" style="width: 100px">消息状态</label>
          <el-row style="margin-left: 100px">
            <el-col :span="5" :offset="1">
              <div class="el-alert el-alert--success is-light">
                <i class="el-alert__icon el-icon-success" />
                <div class="el-alert__content">
                  <span class="el-alert__title">success alert</span>
                  <i class="el-alert__closebtn el-icon-close" />
                </div>
              </div>
            </el-col>

            <el-col :span="5" :offset="1">
              <div class="el-alert el-alert--info is-light">
                <i class="el-alert__icon el-icon-info" />
                <div class="el-alert__content">
                  <span class="el-alert__title">info alert</span>
                  <i class="el-alert__closebtn el-icon-close" />
                </div>
              </div>
            </el-col>

            <el-col :span="5" :offset="1">
              <div class="el-alert el-alert--warning is-light">
                <i class="el-alert__icon el-icon-warning" />
                <div class="el-alert__content">
                  <span class="el-alert__title">warning alert</span>
                  <i class="el-alert__closebtn el-icon-close" />
                </div>
              </div>
            </el-col>

            <el-col :span="5" :offset="1">
              <div class="el-alert el-alert--error is-light">
                <i class="el-alert__icon el-icon-error" />
                <div class="el-alert__content">
                  <span class="el-alert__title">error alert</span>
                  <p class="el-alert__description">more text</p>
                  <i class="el-alert__closebtn el-icon-close" />
                </div>
              </div>
            </el-col>
          </el-row>
        </el-col>
      </el-row>
    </div>

    <div class="dashboard-item">
      <h4>树形结构：</h4>
      <div>
        <el-tree class="dashboard-item-tree" :data="tree" show-checkbox :props="defaultProps" @node-click="handleNodeClick" />
        <p style="display: inline-block;vertical-align: middle">
          tree组件必须保证defaultProps: {children: 'children',label: 'label'}中label 和 children字段的正确性
        </p>
      </div>
    </div>

    <h2><i class="el-icon-set-up" style="margin-right: 10px" />element-ui@2.13.0版本自定义组件和主题修改</h2>

    <div class="dashboard-item">
      <h4>自定义组件样式：</h4>
      <el-row :gutter="12">
        <el-col :span="8">
          <el-card shadow="always">
            <div slot="header" class="clearfix">
              <span>思路介绍</span>
            </div>
            <div style="line-height: 1.5">
              通过使用sass预处理器，定义项目中基础的样式变量，编写变量文件variables.scss，对文件变量值的进行样式的自定义设置，导入这些变量运用于组件样式的赋值
            </div>
          </el-card>
        </el-col>
        <el-col :span="8">
          <el-card shadow="hover">
            <div slot="header" class="clearfix">
              <span>变量定义范例</span>
            </div>

            <div>
              <p>$menuText: #bfcbd9</p>
              <p>通过:export方法导出变量对象</p>
              <p>:export {</p>
              <p>menuText: $menuText</p>
              <p>}</p>
            </div>
          </el-card>
        </el-col>
        <el-col :span="8">
          <el-card shadow="never">
            <div slot="header" class="clearfix">
              <span>使用变量范例</span>
            </div>

            <div>
              <p>$menuText: #bfcbd9</p>
              <p>通过:export方法导出变量对象</p>
              <p>:export {</p>
              <p>menuText: $menuText</p>
              <p>}</p>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>

    <!-- <p :style="colorObj">
      <b>由于更换主题需要外网资源支持，动态主题颜色切换功能仅在外网环境能够预览和使用</b>
    </p> -->

    <!-- <div class="dashboard-item">
      <span class="dashboard-item-label">主题：</span>
      <el-color-picker
        v-model="theme"
        :predefine="['#409EFF', '#1890ff', '#304156','#212121','#11a983', '#13c2c2', '#6959CD', '#f5222d', ]"
        size="small"
        class="dashboard-item-label"
      />
      <span :style="colorObj" class="dashboard-item-label">当前值:{{ defaultTheme }}</span>
      <span class="dashboard-item-label">根据theme主题颜色的变动，修改样式文件中的变量，从而改变主题值</span>
    </div> -->

    <!-- <div class="dashboard-item">
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
    </div> -->

    <!-- <div class="dashboard-item">
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
    </div> -->
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
      colors: [],
      tableData: [
        {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }
      ],
      loading: false,
      dialogVisible: false,
      input: '',
      select: '',
      options: [],
      date: '',

      tree: [{
        label: '一级 1',
        children: [{
          label: '二级 1-1',
          children: [{
            label: '三级 1-1-1'
          }]
        }]
      }, {
        label: '一级 2',
        children: [{
          label: '二级 2-1',
          children: [{
            label: '三级 2-1-1',
            children: [{
              label: '四级 2-1-1-1'
            }]
          }]
        }, {
          label: '二级 2-2',
          children: [{
            label: '三级 2-2-1'
          }]
        }]
      }, {
        label: '一级 3',
        children: [{
          label: '二级 3-1',
          children: [{
            label: '三级 3-1-1'
          }]
        }, {
          label: '二级 3-2',
          children: [{
            label: '三级 3-2-1'
          }]
        }]
      }],
      defaultProps: {
        children: 'children',
        label: 'label'
      }
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
    },
    handleSelectionChange(val) {
      console.log(val)
    },
    handleEdit(index, row) {
      console.log(index, row)
      this.dialogVisible = true
    },
    handleDelete(index, row) {
      console.log(index, row)
    },
    openLoading() {
      this.loading = true
      setTimeout(() => {
        this.loading = false
      }, 2000)
    },
    handleNodeClick(data) {
      console.log('tree:', data)
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
  &-item-form-label {
    float: left;
    width: 60px;
  }
  &-item-form-span {
    float: right;
    width: 120px;
    word-wrap: none;
  }
  &-item-form-label, &-item-form-span {
    vertical-align: middle;
    line-height: 40px;
    text-align: center
  }
  &-item-tree {
    display: inline-block;
    width: 300px;
    vertical-align: middle
  }
}
</style>
