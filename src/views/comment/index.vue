<template>
  <div class="comment-container">
    <el-card class="box-card">
    <div slot="header" class="clearfix">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item to="/">首页</el-breadcrumb-item>
            <el-breadcrumb-item>评论管理</el-breadcrumb-item>
        </el-breadcrumb>
    </div>
      <el-table
        :data="articles"
        style="width: 100%"
        class="table-list"
        stripe>
        <el-table-column
            prop="title"
            label="标题"
            >
        </el-table-column>
        <el-table-column
            prop="total_comment_count"
            label="总评论数"
            >
        </el-table-column>
           <el-table-column
            prop="fans_comment_count"
            label="粉丝评论数"
           >
        </el-table-column>
        <el-table-column
            prop="address"
            label="状态">
            <template slot-scope="scope">
                {{scope.row.comment_status? '正常': '关闭'}}
            </template>
        </el-table-column>
        <el-table-column
            prop="address"
            label="操作">
            <template slot-scope="scope">
                <el-switch
                v-model="scope.row.comment_status"
                @change="onStatusChange(scope.row)"
                active-color="#13ce66"
                :disabled="scope.row.statusDisabled"
                inactive-color="#ff4949">
                </el-switch>
            </template>
        </el-table-column>
      </el-table>
        <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="page"
        :page-size.sync="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="totalCount"
        background>
        </el-pagination>
    </el-card>
  </div>
</template>

<script>
import { getArticles, updateCommentStatus } from '@/api/article'
export default {
  name: 'CommentIndex',
  components: {},
  props: {},
  data () {
    return {
      articles: [],
      totalCount: 0,
      pageSize: 10,
      page: 1
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadArticles()
  },
  mounted () {},
  methods: {
    handleSizeChange () {
      this.loadArticles(1)
    },
    handleCurrentChange (page) {
      this.loadArticles(page)
    },
    loadArticles (page = 1) {
      this.page = page
      getArticles({
        response_type: 'comment',
        page,
        per_page: this.pageSize
      }).then(res => {
        const { results } = res.data.data
        results.forEach(article => {
          article.statusDisabled = false
        })
        this.articles = results
        this.totalCount = res.data.data.total_count
      })
    },
    onStatusChange (article) {
      article.statusDisabled = true
      updateCommentStatus(article.id.toString(), article.comment_status).then(res => {
        article.statusDisabled = false
        this.$message({
          type: 'success',
          message: article.comment_status ? '开启' : '关闭'
        })
      })
    }
  }
}
</script>

<style scoped lang="less">
.table-list {
    margin-bottom: 30px;
}
</style>
