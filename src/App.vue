<template>
  <div class="container">
    <form @submit.prevent="save" class="form">
      <input
        type="text"
        v-model="form.title"
        placeholder="Title"
        class="input"
      /><br />
      <textarea
        v-model="form.content"
        placeholder="Content"
        class="textarea"
      ></textarea
      ><br />
      <button type="submit" class="button">Save</button>
    </form>
    <table class="table">
      <thead>
        <tr>
          <th>Title</th>
          <th>Content</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="article in articles" :key="article.id">
          <td>{{ article.title }}</td>
          <td>{{ article.content }}</td>
          <td>
            <button @click="edit(article)" class="button">Edit</button>
            <button @click="remove(article.id)" class="button delete">
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <button @click="load" class="button load">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from "vue";
import axios from "axios";

export default {
  setup() {
    const form = reactive({
      id: null,
      title: "",
      content: "",
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get("http://localhost:3000/articles");
        articles.value = response.data;
      } catch (error) {
        console.error("Error loading articles:", error);
      }
    }

    async function save() {
      try {
        const url = form.id
  ? `http://localhost:3000/articles/${form.id}`
  : "http://localhost:3000/articles";

        const method = form.id ? "put" : "post";
        const response = await axios[method](url, {
          title: form.title,
          content: form.content,
        });

        if (form.id) {
          articles.value = articles.value.map((article) =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = "";
        form.content = "";
      } catch (error) {
        console.error("Error saving article:", error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
      } catch (error) {
        console.error("Error deleting article:", error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  },
};
</script>

<style scoped>
.container {
  background: url("path/to/your/background-image.jpg") no-repeat center center
    fixed;
  background-size: cover;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.form {
  background: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 5px;
  margin-bottom: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.input,
.textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

.button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.button:hover {
  background-color: #45a049;
}

.button.delete {
  background-color: #f44336;
}

.button.delete:hover {
  background-color: #e31e12;
}

.button.load {
  background-color: #008cba;
}

.button.load:hover {
  background-color: #007bb5;
}

.table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.table th,
.table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
  color: #f1eded;
}

.table th {
  background-color: #4caf50;
  color: white;
}

.table tr:hover {
  background-color: #1bde4f;
}
</style>