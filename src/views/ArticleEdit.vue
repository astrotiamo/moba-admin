<template>
  <div>
    <h1>{{id ? '编辑' : '新建'}}文章</h1>
    <!-- @submit-native-prevent="save" -->
    <el-form label-width="120px"> 
      <el-form-item label="所属分类">
        <el-select v-model="model.categories" multiple>
          <el-option v-for="item in categories" :key="item._id" :value="item._id" :label="item.name"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="标题">
        <el-input v-model="model.title"></el-input>
      </el-form-item>
      <el-form-item label="详情">
        <vue-editor v-model="model.body" 
          useCustomImageHandler 
          @image-added="handleImageAdded"></vue-editor>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" native-type="submit" @click="save">保存</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { VueEditor } from 'vue2-editor'
export default {
  name: "ArticleEdit",
  props: {
    id: {},
  },
  components: {
    VueEditor
  },
  data() {
    return {
      model: {},
      categories: [],
      cate: []
    }
  },
  created() {
    this.id && this.fetch()
    this.fetchCategories()
  },
  methods: {
    type() {
      this.categories.forEach(item => {
        if(item.parent) {
          if(item.parent.name === "新闻资讯"){
            this.cate.push(item)
          }
        }
      })
    },
    async save() {
      let res
      if(this.id) {
        // 编辑
        res = await this.$http.put(`rest/articles/${this.id}`, this.model)
      } else {
        // 新建
        res = await this.$http.post('rest/articles', this.model)
      }
      this.$router.push('/articles/list')
      this.$message.success('保存成功！')
    },
    async fetch() {
      const res = await this.$http.get(`rest/articles/${this.id}`)
      console.log(res);
      this.model = res.data
    },
    async fetchCategories() {
      const res = await this.$http.get(`rest/categories`)
      console.log(res);
      this.categories = res.data
      this.type()
    },
    async handleImageAdded(file, Editor, cursorLocation, resetUploader) {
      // An example of using FormData
      // NOTE: Your key could be different such as:
      // formData.append('file', file)

      const formData = new FormData();
      formData.append("file", file);

      const res = await this.$http.post('upload', formData)
      Editor.insertEmbed(cursorLocation, "image", res.data.url);
      resetUploader();

      // axios({
      //   url: "https://fakeapi.yoursite.com/images",
      //   method: "POST",
      //   data: formData
      // }).then(result => {
      //     const url = result.data.url; // Get url from response
      //     Editor.insertEmbed(cursorLocation, "image", url);
      //     resetUploader();
      // }).catch(err => {
      //     console.log(err);
      // });
    }
  }
}
</script>

<style>

</style>