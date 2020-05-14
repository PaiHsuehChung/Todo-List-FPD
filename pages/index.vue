<template>
  <div class="container">
    <div class="d-flex flex-row justify-content-center">
      <div class="col-md-8">
        <div class="card">
          <div class="card body">
            <ul class="list-group">
              <li class="list-group-item" v-for="(item, index) in contacts">
                <a href="#" @click="editHandler(index)">{{ item.todo_list }}</a>
                <button
                  type="button"
                  class="btn btn-outline-danger"
                  @click="removeHandler(index)"
                >
                  Remove
                </button>
              </li>
            </ul>

            <form>
              <div class="form-group mt-5">
                <input
                  type="text"
                  placeholder="Add a Todo"
                  class="form-control"
                  v-model="input.todo_list"
                />
                <button
                  type="submit"
                  class="btn btn-outline-primary mt-3"
                  @click="submitHandler"
                >
                  Add Todo
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      editIndex: null,
      loading: false,
      contacts: [],
      input: {
        todo_list: ""
      }
    };
  },
  mounted() {
    this.loading = true;
    axios
      .get("http://localhost:8888/contact")
      .then(res => {
        this.contacts = res.data;
        this.loading = false;
      })
      .catch(err => {
        console.log(err);
      });
  },
  methods: {
    submitHandler() {
      if (!this.input.todo_list) {
        return;
      }

      if (this.editIndex === null) {
        axios
          .post("http://localhost:8888/contact", this.input)
          .then(res => {
            this.contacts.push(res.data);
            this.input.todo_list = "";
          })
          .catch(err => {
            console.log(err);
          });
      } else {
        let id = this.contacts[this.editIndex].id;
        axios
          .put("http://localhost:8888/contact/" + id, this.input)
          .then(res => {
            this.contacts[this.editIndex] = res.data;
            this.input.todo_list = "";
            this.editIndex = null;
          })
          .catch(err => {
            console.log(err);
          });
      }
    },
    removeHandler(index) {
      let target = this.contacts[index];
      if (confirm(`Delete ${target.todo_list} ?`)) {
        axios
          .delete("http://localhost:8888/contact/" + target.id)
          .then(res => {
            this.contacts.splice(index, 1);
            this.input.todo_list = "";
          })
          .catch(err => {
            console.log(err);
          });
      }
    },
    editHandler(index) {
      this.editIndex = index;
      this.input.todo_list = this.contacts[index].todo_list;
    }
  }
};
</script>
