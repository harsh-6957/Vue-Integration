<template lang="pug">
  .page-user-post
    .container.mx-auto
      .mb-6
        h2.text-2xl.font-bold.pb-2.mb-5.border-b
          | Thêm mới bài viết
      .clearfix
        div.px-4.py-3.rounded.mb-5(
          v-if="messenge" 
          :class="{'bg-green-200 text-dark' : success}"
        ) 
          p.font-bold
            | {{ messenge }}
          hr.bg-white.my-2.border-white
          p.text-sm <span class="font-bold">Important:</span> resource will not be really updated on the server but it will be faked as if.
        .mt-5(class='md:mt-0 md:col-span-2')
          form.bg-gray-100.bg-opacity-5.rounded-md.pb-5
            BaseInput(
              type='hidden' 
              disabled
              v-model="post.id"
            )
            .px-4.py-5.space-y-6(class='sm:p-6')
              div
                label(for='title')
                  | Tiêu đề
                .mt-1
                  BaseInput(
                    class="bg-transparent"
                    id="title" 
                    name='title' 
                    placeholder='Nhập vào tiêu đề' 
                    v-model.trim="post.title"
                  )
              div
                label(for='body')
                  | Nội dung
                .mt-1
                  BaseTextarea(
                    class="bg-transparent"
                    name='body' 
                    id="body" 
                    rows='15' 
                    placeholder='Nhập vào nội dung' 
                    v-model.trim="post.body"
                  )
              div(v-if="post.user")
                label(for='user_name')
                  | Tác giả
                .mt-1
                  BaseInput(
                    v-model.lazy.trim="post.user.name"
                    disabled
                  )
                  BaseInput(
                    type='hidden' 
                    v-model.lazy.trim="post.user.id"
                  )
            .px-4.py-3.text-center(class='sm:px-6')
              div(v-if="addnew")
                button.btn.btn-save(type='button' @click="save") lưu lại
              div(v-else)
                button.btn.btn-edit(type='button' @click="edit") Cập nhật
</template>

<script>
import { mapState } from "vuex";
export default {
  data() {
    return {
      addnew: true,
      post: {},
      success: "",
      messenge: "",
      showMessenge: null,
    };
  },
  async created() {
    let _this = this;
    await _this.$store
      .dispatch("fetchPostById", _this.$route.params.id)
      .then((response) => {
        _this.post = response;
        _this.addnew = false;
      })
      .catch(() => {
        _this.post = {
          userId: null,
          id: null,
          title: "",
          body: "",
          user: {
            id: this.userLogin.id,
            name: this.userLogin.name,
          },
        };
        _this.addnew = true;
      });
  },
  computed: {
    ...mapState(["userLogin"]),
  },
  methods: {
    save() {
      if (this.post.title && this.post.body) {
        let post = {
          title: this.post.title,
          body: this.post.body,
          userId: this.userLogin.id,
        };
        this.$store
          .dispatch("creatingPost", post)
          .then((response) => {
            this.messenge = "Bạn đã thêm thành công.";
            this.success = true;
            console.table(response);
            let _this = this;
            this.showMessenge = setTimeout(function () {
              _this.messenge = "";
              _this.success = "";
            }, 3000);
          })
          .catch(() => {
            this.messenge = "Thêm thất bại";
          });
      } else {
        alert("Bạn chưa nhập đủ nội dung");
      }
    },
    edit() {
      if (this.post.title && this.post.body) {
        let post = {
          title: this.post.title,
          body: this.post.body,
        };
        this.$store
          .dispatch("updatingPost", this.post.id, post)
          .then((response) => {
            this.messenge = "Bạn đã cập nhật bài viết thành công.";
            this.success = true;
            console.table(response);
            let _this = this;
            this.showMessenge = setTimeout(function () {
              _this.messenge = "";
              _this.success = "";
            }, 3000);
          })
          .catch(() => {
            this.messenge = "Cập nhật thất bại";
          });
      } else {
        alert("Bạn chưa nhập đủ nội dung");
      }
    },
  },
  beforeDestroy() {
    clearTimeout(this.showMessenge);
  },
  watch: {
    $route() {
      console.log("route change");
      // this.$forceUpdate();
      this.$router.go();
    },
  },
};
</script>

<style lang="postcss" scoped>
label {
  @apply block text-sm font-medium;
}
.input {
  @apply shadow-sm focus:ring-indigo-500 focus:border-indigo-500 mt-1 block w-full sm:text-sm border border-gray-300 rounded-md px-2 py-2 outline-none;
}
.btn {
  @apply inline-flex justify-center py-2 px-10 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 uppercase hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 outline-none;
}
.btn.btn-save {
  @apply bg-green-500;
}
.btn.btn-edit {
  @apply bg-yellow-500;
}
</style>
