<template>
  <div>
    <el-upload
      class="attachment-upload"
      ref="upload"
      action=""
      :on-preview="handleOnPreview"
      :on-remove="handleOnRemove"
      :before-upload="handleBeforeUpload"
      :file-list="fileList"
      :limit="limit"
      :on-exceed="handleOnExceed"
      :disabled="disabled || loading"
    >
      <el-button
        icon="el-icon-upload"
        plain
        size="small"
        :disabled="disabled"
        :loading="loading"
        >{{ $t("basic.upload") }}</el-button
      >
      <div slot="tip" class="el-upload__tip">
        {{ $t("UploadText.tip_1", { limit }) }}
      </div>
    </el-upload>
  </div>
</template>

<script>
import { HuaweiObsService } from "@/global/service/huawei";

export default {
  props: {
    value: {
      type: Array,
      default: () => []
    },
    limit: {
      type: Number,
      default: 5
    },
    disabled: {
      type: Boolean,
      default: false
    },
    platform: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      loading: false,
      fileList: []
    };
  },
  watch: {
    value(val) {
      this.fileList = JSON.parse(JSON.stringify(val));
    }
  },
  methods: {
    handleOnPreview(file) {
      window.open(file.url);
    },
    handleOnRemove(file) {
      this.fileList.forEach((data, index, self) => {
        if (file.uid === data.uid) {
          self.splice(index, 1);
        }
      });
      this.$emit("update:value", this.fileList);
    },
    handleBeforeUpload(file) {
      if (!file || this.loading) {
        return false;
      }
      this.loading = true;
      const huaweiService = new HuaweiObsService();
      huaweiService.multipartUpload(
        file,
        () => {},
        this.successCallback,
        this.errorCallback,
        this.platform
      );
      return false;
    },
    successCallback(res, host, file) {
      const url = `${host}/${res.InterfaceResult.Key}`;
      this.fileList.push({ name: file.name, url });
      this.$emit("update:value", this.fileList);
      this.loading = false;
    },
    errorCallback() {
      this.loading = false;
    },
    handleOnExceed() {
      this.$message.warning(
        this.$i18n.t("UploadText.tip_2", { limit: this.limit })
      );
    }
  }
};
</script>

<style lang="less" scoped>
.attachment-upload {
  max-width: 600px;
}
.el-upload__tip {
  line-height: 1.6;
}
</style>
