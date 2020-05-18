<template>
    <div>
      <!--卡片式图-->
      <el-card>
        <el-alert title="添加商品信息" center show-icon :closable="false" type="info">
        </el-alert>
        <!--步骤条组件 :active="0" 指定激活项的索引 : number类型-->
        <el-steps :space="200" :active="activeIndex - 0" finish-status="success" align-center>
          <el-step title="基本信息"></el-step>
          <el-step title="商品参数"></el-step>
          <el-step title="商品属性"></el-step>
          <el-step title="商品图片"></el-step>
          <el-step title="商品内容"></el-step>
          <el-step title="完成"></el-step>
        </el-steps>

        <!--表单结构-->
        <el-form :model="addForm" :rules="addFormRules" ref="addFormRef">
        <!--竖向标签页-->
        <el-tabs :tab-position="'left'" v-model="activeName" @tab-click="tabClick" :before-leave="beforeTabsEvent">
          <el-tab-pane label="基本参数" name="0">
            <!--表单组-->
              <el-form-item label="商品名称" prop="goods_name">
                <el-input v-model="addForm.goods_name"></el-input>
              </el-form-item>
            <el-form-item label="商品价格" prop="goods_price">
              <el-input v-model="addForm.goods_price"></el-input>
            </el-form-item>
            <el-form-item label="商品重量" prop="goods_weight">
              <el-input v-model="addForm.goods_weight"></el-input>
            </el-form-item>
            <el-form-item label="商品数量" prop="goods_number">
              <el-input type="number" v-model="addForm.goods_number"></el-input>
            </el-form-item>
            <el-form-item label="商品分类" prop="goods_cat">
              <!-- 选择商品分类的级联选择框 -->
              <el-cascader
                v-model="addForm.goods_cat"
                :options="cateList"
                :props="cateProps"
                @change="handleChange"
              ></el-cascader>
            </el-form-item>
          </el-tab-pane>
          <el-tab-pane label="商品参数" name="1">配置管理</el-tab-pane>
          <el-tab-pane label="商品属性" name="2">角色管理</el-tab-pane>
          <el-tab-pane label="商品图片" name="3">定时任务补偿</el-tab-pane>
          <el-tab-pane label="商品内容" name="4">定时任务补偿</el-tab-pane>
        </el-tabs>
        </el-form>
      </el-card>
    </div>
</template>

<script>
    export default {
        name: 'Add',
      data() {
          return {
            activeIndex: 0,
            // 标签页激活的值
            activeName: '0',
            // 表单数据对象
            addForm:{
              goods_price:0,
              goods_weight: 0,
              goods_number: 0,
              goods_cat:[]
            },
            // 验证规则对象
            addFormRules:{
              goods_name:[
                { required: true, message: '请输入商品名称', trigger: 'blur' }
              ],
              goods_price:[
                { required: true, message: '请输入商品价格', trigger: 'blur' }
              ],
              goods_weight:[
                { required: true, message: '请输入商品重量', trigger: 'blur' }
              ],
              goods_number:[
                { required: true, message: '请输入商品数量', trigger: 'blur' }
              ],
              goods_cat:[
                { required: true, message: '请选择三级商品分类', trigger: 'change' }
              ]

            },
            cateList:[],
            // 级联选择框的配置对象
            cateProps: {
              value: 'cat_id',
              label: 'cat_name',
              children: 'children',
              expandTrigger:'hover'
            }

          }
      },
      created() {
          this.getCateList()
      },
      methods:{
          // 监听tabs的click事件
        tabClick() {
          this.activeIndex = this.activeName
        },
        // 定义函数获取级联选择器中的数据
        async getCateList() {
          const { data: res } = await this.$http.get('categories')
          if (res.meta.status !== 200) { return this.$message.error('获取商品列表失败') }
          // 成功则保存到data中
          this.cateList = res.data
        },
        // 级联选择器改变就会触发的的函数
        handleChange() {
          if (this.addForm.goods_cat.length !== 3) {
            // 清空数组
            this.addForm.goods_cat = []
          }
        },
        // 监听离开标签页之前的事件
        beforeTabsEvent(newActiveName, oldActiveName) {
          // 判断第一个面板未选择三级分类, 则阻止切换
          if (oldActiveName === '0' && this.addForm.goods_cat.length !== 3) {
            this.$message.error('请选择三级分类')
            return false // 阻止切换
          }
        }

      }
    }
</script>

<style scoped>

</style>
