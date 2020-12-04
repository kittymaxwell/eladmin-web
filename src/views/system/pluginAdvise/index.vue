<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">意见建议</label>
        <el-input v-model="query.content" clearable placeholder="意见建议" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">联系方式</label>
        <el-input v-model="query.contact" clearable placeholder="联系方式" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="意见建议" prop="content">
            <el-input v-model="form.content" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="联系方式" prop="contact">
            <el-input v-model="form.contact" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="createTime" label="创建时间">
          <template slot-scope="scope">
            <span>{{ parseTime(scope.row.createTime) }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="content" label="意见建议" />
        <el-table-column prop="contact" label="联系方式" />
        <el-table-column v-permission="['admin','pluginAdvise:edit','pluginAdvise:del']" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudPluginAdvise from '@/api/pluginAdvise'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, createTime: null, updateTime: null, createUser: null, updateUser: null, deleteFlag: null, content: null, contact: null }
export default {
  name: 'PluginAdvise',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '意见建议', url: 'api/pluginAdvise', idField: 'id', sort: 'id,desc', crudMethod: { ...crudPluginAdvise }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'pluginAdvise:add'],
        edit: ['admin', 'pluginAdvise:edit'],
        del: ['admin', 'pluginAdvise:del']
      },
      rules: {
        id: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        content: [
          { required: true, message: '意见建议不能为空', trigger: 'blur' }
        ],
        contact: [
          { required: true, message: '联系方式不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'content', display_name: '意见建议' },
        { key: 'contact', display_name: '联系方式' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
