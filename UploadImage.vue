<template>
  <div class="upload-container" :class="uploadWidth" v-loading="uploadLoading">
    <el-upload
      action=""
      accept="image/jpeg image/gif image/png"
      :show-file-list="false"
      :on-preview="handleOnPreview"
      :before-upload="handleBeforeUpload"
      :disabled="isDisabled"
    >
      <img
        style="width: 100%; height: 100%"
        v-if="cover_url"
        :src="cover_url"
      />
      <i v-else class="el-icon-plus upload-icon"></i>
    </el-upload>
  </div>
</template>

<script>
import qiniuService from "@/global/service/qiniu";

export default {
  props: {
    space: {
      type: String,
      default: ""
    },
    folder: {
      type: String,
      default: "admin"
    },
    api: {
      type: String,
      default: ""
    },
    uploadWidth: {
      type: String,
      default: ""
    },
    uploadLimit: {
      type: Number,
      default: 500
    },
    cover_url: {
      type: String,
      default: ""
    },
    isDisabled: {
      type: Boolean,
      default: false
    },
    cover_file_id: {
      type: Number,
      default: null
    }
  },
  data() {
    return {
      uploadLoading: false
    };
  },
  methods: {
    handleOnPreview(file) {
      window.open(file.url);
    },
    handleBeforeUpload(file) {
      if (!file || !this.validateSize(file)) {
        return false;
      }
      const space = this.space;
      const folder = this.folder;
      const api = this.api;
      this.uploadLoading = true;
      qiniuService
        .upload({ space, folder, file, api }, () => {})
        .then(res => {
          this.$emit("update:cover_url", res.url);
          this.$emit("update:cover_file_id", res.id);
        })
        .finally(() => {
          this.uploadLoading = false;
        });
      return false;
    },
    validateSize(file) {
      const size = this.uploadLimit * 1024;
      const fileName = file.name;
      const suffix = fileName.split(".").pop();
      const imageRegex = /(jpg|gif|png)/;
      if (file.size > size || !imageRegex.test(suffix)) {
        if (this.uploadLimit < 1024) {
          this.$message.error(
            `请上传不大于 ${this.uploadLimit}KB 且格式为jpg、gif、png 的文件`
          );
        } else {
          this.$message.error(
            `请上传不大于 ${this.uploadLimit /
              1024}MB 且格式为jpg、gif、png 的文件`
          );
        }
        return false;
      }
      return true;
    }
  }
};
</script>

<style lang="less" scoped>
.upload-container {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  overflow: hidden;
}
.upload-w-44 {
  width: 44px;
  height: 44px;
  .upload-icon {
    width: 44px;
    height: 44px;
    font-size: 12px;
    line-height: 44px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-1920 {
  width: 480px;
  height: 150px;
  .upload-icon {
    width: 480px;
    height: 150px;
    font-size: 28px;
    line-height: 150px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-800 {
  width: 200px;
  height: 125px;
  .upload-icon {
    width: 200px;
    height: 125px;
    font-size: 28px;
    line-height: 125px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-140 {
  width: 140px;
  height: 140px;
  .upload-icon {
    width: 140px;
    height: 140px;
    font-size: 28px;
    line-height: 140px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-134 {
  width: 134px;
  height: 134px;
  .upload-icon {
    width: 134px;
    height: 134px;
    font-size: 28px;
    line-height: 134px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-397 {
  width: 397px;
  height: 252px;
  .upload-icon {
    width: 397px;
    height: 252px;
    font-size: 28px;
    line-height: 252px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-295 {
  width: 295px;
  height: 170px;
  .upload-icon {
    width: 295px;
    height: 170px;
    font-size: 28px;
    line-height: 170px;
    text-align: center;
    color: #8c939d;
  }
}
.upload-w-610 {
  width: 610px;
  height: 170px;
  .upload-icon {
    width: 610px;
    height: 170px;
    font-size: 28px;
    line-height: 170px;
    text-align: center;
    color: #8c939d;
  }
}
</style>
