<template>
  <q-page class="padding">
    <div class="row">
      <div class="col-xs-12 col-sm-6 col-md-6">
        <h2>Create a Post here</h2>

        <div class="q-pa-md" style="max-width: 400px">
          <q-form @submit="post" class="q-gutter-md" method="post">
            <q-input
              filled
              v-model="insertposts.userid"
              label="User ID*"
              hint="Your ID"
              lazy-rules
              :rules="[
                (val) => (val && val.length > 0) || 'Please type something',
              ]"
            />

            <q-input
              filled
              v-model="insertposts.title"
              label="Title of Post*"
              hint="The title you want to give the post"
              lazy-rules
              :rules="[
                (val) => (val && val.length > 0) || 'Please type something',
              ]"
            />

            <q-input
              filled
              type="textarea"
              v-model="insertposts.content"
              label="Post Content*"
              hint="The content you want to put for the post"
              lazy-rules
              :rules="[
                (val) => (val && val.length > 0) || 'Please type something',
              ]"
            />

            <div>
              <q-btn label="Submit" type="submit" color="primary" />
              <q-btn
                label="Reset"
                type="reset"
                color="primary"
                flat
                class="q-ml-sm"
              />
            </div>
          </q-form>
        </div>
      </div>
      <div class="col-xs-12 col-sm-6 col-md-6">
        <h2>Displayed Information</h2>

        <!--Q-dialog for success messages-->
        <q-dialog v-model="successdialog">
          <q-card class="bg-secondary successcard">
            <div align="center" class="successmessage">{{ dialogmessage }}</div>
          </q-card>
        </q-dialog>

        <!--Q-dialog for unsuccesful actions-->
        <q-dialog v-model="errordialog">
          <q-card class="bg-red error">
            <div align="center" class="errormessage">
              {{ dialogmessage }}
            </div>
          </q-card>
        </q-dialog>

        <!--Q-dialog for Updating posts-->
        <q-dialog v-model="updatedialog" persistent>
          <q-card style="min-width: 350px">
            <q-card-section>
              <div class="text-h6">Update Posts</div>
            </q-card-section>

            <q-card-section class="q-pt-none">
              <q-form
                @submit="updatepost(id)"
                class="q-gutter-md"
                method="post"
              >
                <q-input
                  filled
                  v-model="insertposts.userid"
                  label="User ID*"
                  hint="Your ID"
                  lazy-rules
                  :rules="[
                    (val) => (val && val.length > 0) || 'Please type something',
                  ]"
                />

                <q-input
                  filled
                  v-model="insertposts.title"
                  label="Title of Post*"
                  hint="The title you want to give the post"
                  lazy-rules
                  :rules="[
                    (val) => (val && val.length > 0) || 'Please type something',
                  ]"
                />

                <q-input
                  filled
                  type="textarea"
                  v-model="insertposts.content"
                  label="Post Content*"
                  hint="The content you want to put for the post"
                  lazy-rules
                  :rules="[
                    (val) => (val && val.length > 0) || 'Please type something',
                  ]"
                />

                <div>
                  <q-btn label="Submit" type="submit" color="primary" />
                  <q-btn
                    label="Reset"
                    type="reset"
                    color="primary"
                    flat
                    class="q-ml-sm"
                  />
                </div>
              </q-form>
            </q-card-section>

            <q-card-actions align="right" class="text-primary">
              <q-btn flat label="Cancel" v-close-popup />
              <q-btn flat label="Add address" v-close-popup />
            </q-card-actions>
          </q-card>
        </q-dialog>
        <q-btn
          color="primary"
          label="Fetch Data"
          @click="fetchposts"
          class="fetchbtn"
        />

        <div class="q-pa-md">
          <q-table
            title="Posts"
            :loading="loading"
            :columns="[
              {
                name: 'Id',
                label: 'ID',
                align: 'left',
              },

              {
                name: 'posttitle',
                label: 'Title',
                align: 'left',
              },

              {
                name: 'postcontent',
                label: 'Body',
                align: 'left',
              },

              {
                name: 'action',
                label: 'Action',
                align: 'left',
              },
            ]"
            :rows="posts"
            :pagination="{
              rowsPerPage: 1,
            }"
          >
            <template #body="props">
              <q-tr :props="props" v-for="post in posts[0]" :key="post.id">
                <q-td key="id">{{ post.id }}</q-td>
                <q-td key="posttitle">{{ props.row[0].title }}</q-td>
                <q-td key="postcontent">{{ props.row[0].body }}</q-td>
                <q-td key="action"
                  ><q-btn
                    color="primary"
                    icon="edit"
                    @click="updatedialog = true"
                /></q-td>
                <q-td key="action"
                  ><q-btn
                    color="primary"
                    icon="delete"
                    @click="deletepost(post.id)"
                /></q-td>
              </q-tr>
            </template>
          </q-table>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import { useQuasar } from "quasar";
import axios from "axios";

export default defineComponent({
  name: "IndexPage",

  data() {
    return {
      posts: [],
      insertposts: {
        userid: "",
        title: "",
        content: "",
      },
      loading: false,
      successdialog: false,
      errordialog: false,
      dialogmessage: "",
      updatedialog: false,
    };
  },

  methods: {
    post() {
      axios
        .post("https://jsonplaceholder.typicode.com/posts/", this.insertposts)
        .then((response) => {
          this.successdialog = true;
          this.dialogmessage = "Succesful";
          console.log(response.data);
        })
        .catch((error) => {
          this.dialogmessage =
            "Whoops something went wrong please try again, if probelm persists refresh page";
          this.errordialog = true;
        });
    },

    fetchposts() {
      this.loading = true;
      axios
        .get("https://jsonplaceholder.typicode.com/posts")
        .then((response) => {
          this.loading = false;
          this.posts.push(response.data);
          this.successdialog = true;
          this.dialogmessage = "Succesful";
        })
        .catch((error) => {
          this.errordialog = true;
          this.dialogmessage =
            "Whoops something went wrong please try again, if probelm persists refresh page";
          this.loading = false;
        });
    },

    updatepost(id) {
      axios
        .patch(
          "https://jsonplaceholder.typicode.com/posts/" + id,
          this.insertposts
        )
        .then((response) => {
          this.successdialog = true;
          this.dialogmessage = "Succesful";
          console.log(response);
        })
        .catch((error) => {
          console.log(error);
          this.errordialog = true;
          this.dialogmessage =
            "Whoops something went wrong please try again, if probelm persists please refresh page";
        });
    },

    deletepost(id) {
      axios
        .delete("https://jsonplaceholder.typicode.com/posts/" + id)
        .then((response) => {
          console.log(response);
          this.successdialog = true;
          this.dialogmessage = "Succesful";
          this.fetchposts();
        })
        .catch((error) => {
          console.log(error);
          this.errordialog = true;
          this.dialogmessage =
            "Whoops something went wrong please try again, if probelm persists please refresh page";
        });
    },
  },
});
</script>

<style scoped>
.fetchbtn {
  margin-left: 14px;
}

.successmessage {
  color: white;
  margin-top: 10px;
}
.error {
  color: white;
  height: 50px;
  width: 50%;
}
.successcard {
  width: 150px;
  height: 40px;
}
.errormessage {
  margin-top: 10px;
}
</style>
