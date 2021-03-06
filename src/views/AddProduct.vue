<template>
  <!-- 对话框组件 -->
  <el-dialog
    title="添加商品信息"
    v-model="centerDialogVisible"
    width="60%"
    center
    @close="closeDialog(false)"
  >
    <!-- 表单组件 -->
    <el-form
      :model="goodsForm"
      :rules="rules"
      ref="validateForm"
      label-width="100px"
    >
      <!-- 表单项 -->
      <el-form-item label="商品名称" prop="title">
        <el-input v-model="goodsForm.title"></el-input>
      </el-form-item>

      <!-- 表单项 -->
      <el-form-item label="商品价格" prop="price">
        <el-input v-model="goodsForm.price"></el-input>
      </el-form-item>

      <!-- 表单项 -->
      <el-form-item label="商品图片" prop="coverImg">
        <!-- 图片上传
            action属性：指定上传的服务端地址,修改时需要绑定
         -->
        <el-upload
          class="avatar-uploader"
          :action="uploadURL"
          :show-file-list="false"
          :on-success="handleAvatarSuccess"
        >
          <!-- 图片上传以后 -->
          <img v-if="imageUrl" :src="imageUrl" class="avatar" />
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>

      <!-- 表单项 -->
      <el-form-item label="商品详情" prop="goodsDeatil">
        <!-- 富文本编辑器组件 -->
        <QuillEditor
          theme="snow"
          v-model="goodsForm.goodsDeatil"
          style="height: 100px"
        />
      </el-form-item>

      <!-- 表单项 -->
      <el-form-item>
        <el-button type="danger" @click="submitAdd">添加商品</el-button>
      </el-form-item>
    </el-form>

    <!-- 底部操作 -->
    <template #footer>
      <span class="dialog-footer">
        <!-- 取消按钮事件 -->
        <el-button
          @click="
            () => {
              closeDialog(false)
            }
          "
        >
          取 消
        </el-button>
        <el-button type="primary" @click="centerDialogVisible = false">
          确 定
        </el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script>
import { reactive, toRefs, ref } from 'vue'
// 导入富文本编辑器
import { QuillEditor } from '@vueup/vue-quill'
import '@vueup/vue-quill/dist/vue-quill.snow.css'

// 封装函数：添加商品
function useAdd(validateForm) {
  const submitAdd = () => {
    validateForm.value.validate((valid) => {
      console.log(valid)
    })
  }
  return {
    submitAdd,
  }
}

export default {
  // 注册富文本
  components: {
    QuillEditor,
  },
  // 接收属性
  props: {
    // 判断类型
    centerDialogVisible: Boolean,
  },

  // 触发属性,声明来自父组件定义的事件
  emits: ['onCloseDialog'],

  // 使用属性
  setup(props, { emit }) {
    // 校验表单
    const validateForm = ref()
    // 响应式对象
    const state = reactive({
      // 商品窗口
      centerDialogVisible: props.centerDialogVisible,

      // 图片上传服务端地址:字符串拼接
      uploadURL: import.meta.env.VITE_APP_URL + '/goods/fileUpload',
      // 图片上传以后路径
      imageUrl: '',

      // 商品表单
      goodsForm: {
        title: '',
        price: 0,
        coverImg: '',
        goodsDetail: '',
      },
    })

    // 关闭函数
    const closeDialog = (visible) => {
      // 清空图片地址
      state.imageUrl = ''

      // 子传父：触发事件
      emit('onCloseDialog', visible)
    }
    // 图片上传成功后处理函数: 服务端返回
    const handleAvatarSuccess = (response) => {
      // 图片完整路径
      state.imageUrl = import.meta.env.VITE_APP_URL + response.msg
      state.goodsForm.coverImg = response.msg
      // console.log(response.msg)
    }

    // 校验规则
    const rules = {
      // 名称
      title: [{ required: true, message: '请输入商品名称', trigger: 'blur' }],
      // 价格
      price: [{ required: true, message: '请输入商品价格', trigger: 'blur' }],
      // 图片
      coverImg: [
        { required: true, message: '请上传商品主图', trigger: 'blur' },
      ],
      // 详情
      goodsDeatil: [
        { required: true, message: '请输入商品详情', trigger: 'blur' },
      ],
    }

    return {
      // 结构方式
      ...toRefs(state),
      closeDialog,
      handleAvatarSuccess,
      rules,
      ...useAdd(validateForm),
      validateForm,
    }
  },
}
</script>

<style>
/* 图片上传样式：用户头像 */
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
