<template>
  <div>
    <h1>{{id ? '编辑' : '新建'}}物品</h1>
    <!-- @submit-native-prevent="save" -->
    <el-form label-width="120px"> 
      <!-- <el-form-item label="上级分类">
        <el-select v-model="model.parent">
          <el-option v-for="item in parents" :key="item._id" :value="item._id" :label="item.name"></el-option>
        </el-select>
      </el-form-item> -->
      <el-form-item label="名称">
        <el-input v-model="model.name"></el-input>
      </el-form-item>
      <el-form-item label="图标">
        <!-- mixin getAuthHeaders() uploadUrl -->
        <el-upload
          class="avatar-uploader"
          :action="uploadUrl"
          :headers="getAuthHeaders()"
          :show-file-list="false"
          :on-success="afterUpload">
          <img v-if="model.icon" :src="model.icon" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" native-type="submit" @click="save">保存</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: "ItemEdit",
  props: {
    id: {}
  },
  data() {
    return {
      model: {
        name: '',
        icon: ''
      },
    }
  },
  created() {
    this.id && this.fetch()
  },
  methods: {
    afterUpload(res) {
      // console.log(res);
      this.model.icon = res.url
      // this.$set(this.model, 'icon', res.url)
    },
    async save() {
      let res
      if(this.id) {
        // 编辑
        res = await this.$http.put(`rest/items/${this.id}`, this.model)
      } else {
        // 新建
        res = await this.$http.post('rest/items', this.model)
      }
      this.$message.success('保存成功！')
      this.$router.push('/items/list')
    },
    async fetch() {
      const res = await this.$http.get(`rest/items/${this.id}`)
      console.log(res);
      this.model = res.data
    },
  }
}
</script>

<style>
  
</style>