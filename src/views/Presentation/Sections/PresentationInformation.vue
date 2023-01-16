<script setup>
import DefaultInfoCard from "../../../examples/cards/infoCards/DefaultInfoCard.vue";
import MaterialButton from "@/components/MaterialButton.vue";
import DefaultCounterCard from "../../../examples/cards/counterCards/DefaultCounterCard.vue";
</script>
<template>
  <section class="my-5 py-5">
    <div class="container">
      <div class="container">
        <div class="row">
          <div class="col-lg-9 z-index-2 border-radius-xl mx-auto py-3">
            <div class="row">
              <div class="col-md-4 position-relative">
                <DefaultCounterCard
                  color="success"
                  :title="title"
                  description=""
                  :count="calorie"
                  :suffix="suffix"
                  :duration="3000"
                  divider="vertical"
                />
              </div>
              <div class="col-md-4 position-relative">
                <DefaultCounterCard
                  color="info"
                  title="Info"
                  description="This represents the percentage of a balanced diet of 2000 Kcal per day"
                  :count="(calorie / 2000) * 100"
                  suffix="%"
                  :duration="3000"
                  divider="vertical"
                />
              </div>
              <div class="col-md-4 position-relative">
                <DefaultCounterCard
                  color="warning"
                  title="Info"
                  description="Keep in mind that you only have this amount of Kcal left for this day. Have good health."
                  :count="2000 - calorie"
                  suffix="Kcal"
                  :duration="3000"
                  divider="vertical"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row align-items-center">
        <div class="col-lg-4 ms-auto me-auto p-lg-4 mt-lg-0 mt-4">
          <img
            class="max-width-50 w-100 position-relative z-index-2"
            :src="image"
            alt="image"
          />
        </div>
        <div class="col-lg-6 ms-auto">
          <div class="row justify-content-start">
            <DefaultInfoCard
              icon="flip_to_front"
              title="Upload Image"
              description="Attach any image that would like to know the number of calories it has"
            />
            <input
              style="display: none"
              ref="fileInput"
              type="file"
              @change="onFileSelected"
            />
          </div>
          <div class="row justify-content-start mt-5">
            <div class="container">
              <button class="upload-btn" @click="$refs.fileInput.click()">
                <MaterialButton variant="gradient" color="secondary"
                  >Pick Photo</MaterialButton
                >
              </button>
              <button class="upload-btn" @click="onUpload">
                <MaterialButton variant="gradient" color="success"
                  >Upload</MaterialButton
                >
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import axios from "axios";

export default {
  name: "HelloWorld",
  data() {
    return {
      selectedFile: null,
      littleImage: "src/assets/img/little-background.jpeg",
      title: "",
      calorie: "",
      suffix: "",
    };
  },
  computed: {
    image() {
      return this.littleImage;
    },
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
      let reader = new FileReader();
      reader.onload = (e) => {
        this.littleImage = e.target.result;
      };
      reader.readAsDataURL(this.selectedFile);
    },
    onUpload() {
      const fd = new FormData();
      fd.append("image", this.selectedFile, this.selectedFile.name);
      axios.post("http://127.0.0.1:5000/predict", fd).then((res) => {
        if (res.status === 200) {
          let data = res.data.prediction[0];
          this.title = data.label;
          this.calorie = data.calories;
          this.suffix = "Kcal";
        }
      });
    },
  },
};
</script>

<style scoped>
.upload-btn {
  border: 0;
  background: white;
}
</style>
