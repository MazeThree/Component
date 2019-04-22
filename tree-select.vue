<!-- 
@author wupeng
@date   19.4.11
@description 封装的树形选择器组件
-->
<template>
  <el-select 
    ref="select" 
    v-model="checkData" 
    :multiple="showCheckbox"
    :clearable="!showCheckbox"
    collapse-tags 
    class="selectBody" 
    placeholder="请选择">
    <el-option :value="valueTitle" :label="valueTitle">
      <el-tree
        ref="selectTree"
        :data="data"
        :props="props"
        :node-key="props.value"    
        :show-checkbox="showCheckbox"
        @check="handleClick"
        @node-click="handleNodeClick">
      </el-tree>
    </el-option>
  </el-select>
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
    showCheckbox: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    value: {
      type: [Array, String, Object, Number],
      default: () => []
    }
  },
  data () {
    return {
      filterText: '',
      valueTitle: null,
      checkData: []
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
        this.checkData ? this.$emit('input', this.$refs.selectTree.getCurrentKey()) : this.$emit('input', '')
      }
    }
  },
  methods: {
    handleClick (data, data2) {
      // 不能直接传选中节点的name，因为不知道哪个是对应的label，所以用this.props.labek
      this.valueTitle = this.checkData.length ? this.checkData[0][this.props.label] : ''
      this.checkData = data2.checkedNodes
      this.$emit('input', data)
    },
    handleNodeClick (data) {
      console.log(data)
      if (!this.showCheckbox) {
        this.checkData = ''
        this.checkData = data[this.props.label]
        // this.$emit('input', data)
      }
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
</style>
