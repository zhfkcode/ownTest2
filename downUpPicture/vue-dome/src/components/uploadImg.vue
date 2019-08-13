<template>
    <div class="upload-wrapper">
        <div class="put">
            <input type="file" @change="getFiles" multiple>
        </div>
        <div class="demo-upload-list" v-for="(item,index) in uploadList" :key="index">
            <!-- <template v-if="item.status === 'finished'"> -->

            <img :src="item">
            <span>{{item}}</span>
            <div class="demo-upload-list-cover">
                <Icon type="ios-trash-outline" @click.native="handleRemove(index)"></Icon>
            </div>
            <!-- </template> -->
            <!-- <template v-else> -->
            <!-- <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress> -->

            <!-- </template> -->
        </div>
        <span class="picnum">{{picNum}}/5</span>
        <span v-if="leastPic" class="ivu-form-item-error-tip">请上传至少一张截图！</span>
    </div>
</template>
<script>
export default {
  data() {
    return {
      uploadList: [],
      leastPic: false,
      picNum: 0
    };
  },
  methods: {
      getFiles(e){
          let files= e.target.files;
          console.log(files)
          if(files.length+this.uploadList.lenght>5){//控制上传图片数量\
            this.$Message.error('上传数量超出')
            return 
          } 
            for(let i=0; i<files.lenght; i++){
                
                this.handleBeforeUpload(files[i])
            }
      },
    handleBeforeUpload(file) {
        console.log(file)
      const imgType = " image/jpeg, image/png, image/jpg";
      let form = new FormData();
      form.append("image", file, file.name);
      if (imgType.indexOf(file.type) == -1) {
        this.$Message.error("请选择我们支持的图片格式！");
        return false;
      }
      if (file.size > 2097152) {
        this.$Message.error("请选择2M以内的图片！");
        return false;
      }
      this.upTimes += 1;
      if (this.upTimes > 5 || this.uploadList.length > 4) {
        this.$Message.error("上传文件最多5张！");
        return false;
      }
      axios
        .post("/api/v1/auxi/image", form, {
          headers: {
            Authorization: "Bearer " + cutomToken
          }
        })
        .then(
          response => {
            if (response.data.code === 200) {
              this.uploadList.push(response.data.data.image);
            } else {
              this.upTimes -= 1;
              this.$Message.error("请求失败，请检查网络");
            }
          },
          () => {
            this.$Message.error("请求失败，请检查网络");
          }
        );
      return false;
    },
  }
};
</script>
<style lang="scss" scoped>
</style>
