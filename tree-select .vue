<!-- 
@author wupeng
@date   19.4.11
@description 封装的树形选择器组件
-->
<template>
  <div>
    <i class="el-input__icon el-icon-circle-close top" :class="{ show: hasData }" @click="reset"></i>
    <el-select 
      ref="select" 
      v-model="checkData" 
      :multiple="showCheckbox"
      :multiple-limit="multipleLimit"
      :clearable="!showCheckbox"
      collapse-tags 
      class="selectBody" 
      :placeholder="placeholder"
      @visible-change="mouseleave">
      <el-option :value="valueTitle" :label="valueTitle">
        <el-tree
          ref="selectTree"
          :data="data"
          :props="props"
          :node-key="props.value"    
          :show-checkbox="showCheckbox"
          @check="handleCheck"
          @node-click="handleNodeClick"
          @init="init">
        </el-tree>
      </el-option>
    </el-select>
  </div>
</template>

<script>
export default {
  name: 'asp-tree-select',
  props: {
    data: {
      type: Array,        // 必须是树形结构的对象数组
      default: () => {
        return []
      }
    },
    props: {
      type: Object,
      default: () => {
        return {
          value: 'dictId',        // ID字段名
          label: 'title',         // 显示名称
          children: 'children'    // 子级字段名
        }
      }
    },
    placeholder: {
      type: String,
      default: '请选择'
    },
    // 最大选择数量,多选可用
    multipleLimit: {
      type: Number,
      default: 0
    },
    // 是否显示多选
    showCheckbox: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    // 是否只选子节点
    checkLeaf: {
      type: Boolean,
      default: false
    },
    value: {
      type: [Array, String, Object, Number],
      default: () => []
    },
    defaultExpandedKeys: {
      type: Array,
      default: () => {
        return []
      }
    },
    defaultCheckedKeys: {
      type: Array,
      default: () => {
        return []
      }
    }
  },
  data () {
    return {
      filterText: '',
      valueTitle: null,
      checkData: [],
      // 是否显示清空图标
      hasData: false,
      // 设置默认值
      valueId: this.value,
      // 是否是父级传值
      flag: true
    }
  },
  watch: {
    // 监听实时改变文本框的值
    checkData (val) {
      if (this.showCheckbox) {
        this.$refs.selectTree.setCheckedNodes(this.checkData)
        this.$emit('input', this.$refs.selectTree.getCheckedKeys())
        this.valueTitle = this.checkData.length ? this.checkData[0][this.props.label] : ''
      } else {
        this.$emit('input', '')
        if (this.flag && this.checkData) {
          this.$emit('input', this.$refs.selectTree.getCurrentKey())
        }
      }
    },
    // 监听值的变化调用进行清空操作
    value (newValue, oldValue) {
      if (newValue === '') {
        this.reset()
      }
      // this.$emit('input', this.$refs.selectTree.getCheckedKeys())
      // this.valueTitle = this.checkData.length ? this.checkData[0][this.props.label] : ''
    }
  },
  methods: {
    handleCheck (data, data2) {
      // 不能直接传选中节点的name，因为不知道哪个是对应的label，所以用this.props.label
      this.valueTitle = this.checkData.length ? this.checkData[0][this.props.label] : ''
      this.checkData = data2.checkedNodes
      this.$emit('input', data)
      this.$emit('check', this.$refs.selectTree.getCheckedNodes())
    },
    handleNodeClick (data, data1, data2) {
      if (!this.showCheckbox) {
        if (this.checkLeaf) {
          if (data1.isLeaf) {
            this.flag = true
            this.checkData = ''
            this.checkData = data[this.props.label]
            // this.$emit('input', data)
          }
        } else {
          this.flag = true
          this.checkData = ''
          this.checkData = data[this.props.label]
          // this.$emit('input', data)
        }
      }
      this.$emit('node-click', data, data1, data2)
    },
    // 默认传值方法
    init (code, label) {
      if (this.showCheckbox) {
        this.$refs.selectTree.setCheckedKeys(code)
        this.checkData = this.$refs.selectTree.getCheckedNodes()
      } else {
        this.flag = false
        this.checkData = label
        this.$emit('input', code)
      }
    },
    reset () {
      this.valueTitle = ''
      if (this.showCheckbox) {
        this.checkData = []
        this.hasData = false
        this.$refs.selectTree.setCheckedNodes(this.checkData)
      } else {
        this.checkData = ''
      }
    },
    // 下拉是否展开，对应显示清空
    mouseleave () {
      if (this.valueTitle) {
        this.hasData = true
      } else {
        this.hasData = false
      }
    },
    // 移除某个节点
    remove (data) {
      this.$refs.selectTree.remove(data)
    }
  }
}
</script>

<style lang="scss" scoped>
  .selectBody {
    width: 100%;
  }
  .el-scrollbar .el-scrollbar__view .el-select-dropdown__item{
    height: auto;
    padding: 0;
  }
  /deep/ .el-tree {
    .el-tree-node__label {
    font-size: 12px;
    }
  }
  .el-select-dropdown__item.selected {
    font-weight: normal
  }
  .top {
    display: none;
    position: absolute;
    color: #C0C4CC;
    cursor: pointer;
    font-size: 14px;
    z-index: 999;
    right: 5px;
  }
  .show {
    display: none;
  }
  /deep/ .el-select .el-tag__close.el-icon-close {
      display: none;
  }
</style>
