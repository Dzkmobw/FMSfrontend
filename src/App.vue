<template>
  <div style="margin: 0 auto;width: 50%;">
    <h1 style="text-align: center">球员管理系统</h1>
    <!--  添加按钮 -->
    <el-button type="primary" @click="add_dialog_visible = true" size="small">添加球员</el-button>
    <!-- 数据表格 -->
    <el-table :data="players" style="margin: 20px auto;">
      <el-table-column label="编号" prop="player_number"/>
      <el-table-column label="姓名" prop="player_name"/>
      <el-table-column label="年龄" prop="player_age"/>
      <el-table-column label="位置" prop="player_position"/>
      <el-table-column label="进球" prop="player_goals"/>
      <el-table-column label="助攻" prop="player_assists"/>
      <el-table-column align="right" label="操作" width="200px">
        <template #default="scope">
          <el-button size="small" @click="handleEdit(scope.$index, scope.row)">
            编辑
          </el-button>
          <el-button
              size="small"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
          >
            删除
          </el-button
          >
        </template>
      </el-table-column>
    </el-table>
  </div>

  <!-- 添加页面 -->
  <el-dialog
      title="添加球员"
      v-model="add_dialog_visible"
      width="30%"
      :before-close="handleClose"
  >
    <el-form
        ref="ruleFormRef"
        :model="player_form"
        status-icon
        label-width="120px"
        class="demo-ruleForm"
    >
      <el-form-item label="编号" prop="player_number">
        <el-input v-model="player_form.player_number" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="姓名" prop="player_name">
        <el-input v-model="player_form.player_name" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="年龄" prop="player_age">
        <el-input v-model="player_form.player_age" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="位置" prop="player_position">
        <el-input v-model.number="player_form.player_position" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="进球" prop="player_goals">
        <el-input v-model="player_form.player_goals" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="助攻" prop="player_assists">
        <el-input v-model="player_form.player_assists" autocomplete="off"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm(ruleFormRef)">提交</el-button>
        <el-button @click="resetForm(ruleFormRef)">重置</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
  <!-- 编辑页面 -->
  <el-dialog
      title="编辑球员"
      v-model="edit_dialog_visible"
      width="30%"
      :before-close="handleClose"
  >
    <el-form
        ref="editFormRef"
        :model="player_form"
        status-icon
        label-width="120px"
        class="demo-ruleForm"
    >
      <el-form-item label="编号" prop="player_number">
        <el-input v-model="player_form.player_number" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="姓名" prop="player_name">
        <el-input v-model="player_form.player_name" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="年龄" prop="player_age">
        <el-input v-model="player_form.player_age" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="位置" prop="player_position">
        <el-input v-model.number="player_form.player_position" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="进球" prop="player_goals">
        <el-input v-model="player_form.player_goals" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="助攻" prop="player_assists">
        <el-input v-model="player_form.player_assists" autocomplete="off"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitEditForm(editFormRef)">提交</el-button>
        <el-button @click="resetForm(editFormRef)">重置</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script setup>
import axios from 'axios'
import {reactive, ref, onMounted} from "vue";
//import {ElMessageBox} from 'element-plus'


const players = reactive([])
const getPlayers = () => {
  axios.get("http://localhost:5000/players",).then(res => {
    players.splice(0, players.length)
    players.push(...res.data.results)
    console.log('更新数据')
  })
}
// 页面渲染之后添加数据
onMounted(() => {
  getPlayers()
})

// 删除数据
const handleDelete = (index, scope) => {
  axios.delete(`http://localhost:5000/players/${scope.id}`).then(() => {
    getPlayers()
  })
}


/*表单添加*/
const add_dialog_visible = ref(false)
const ruleFormRef = ref()
const player_form = reactive({
  player_number: "",
  player_name: "",
  player_age: "",
  player_position: "",
  player_goals: "",
  player_assists: "",
  id: "",
})
// 表单提交事件
const submitForm = (formEl) => {
  axios.post('http://localhost:5000/players', player_form).then(() => {
    add_dialog_visible.value = false
    formEl.resetFields()
    getPlayers()
  })
}
// 重置表单
const resetForm = (formEl) => {
  formEl.resetFields()
}

// 关闭弹窗前确认
/*
const handleClose = (done) => {
  ElMessageBox.confirm('确认关闭？')
      .then(() => {
        done();
      })
      .catch(() => {
      });
}*/

/*编辑表单*/
const editFormRef = ref()
const edit_dialog_visible = ref(false)
const handleEdit = (index, scope) => {
  for (let key in scope) {
    player_form[key] = scope[key]
  }
  edit_dialog_visible.value = true
}
// 编辑提交按钮
const submitEditForm = (formEl) => {
  axios.put(`http://localhost:5000/players/${player_form.id}`, player_form).then((res) => {
    formEl.resetFields()
    edit_dialog_visible.value = false
    getPlayers()
  })
}
</script>

<style>
</style>
