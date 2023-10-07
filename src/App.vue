<template>
  <div class="app">
    <div class="containerPost">
      <h1>Страница с постами</h1>
      <my-input v-model="searchQuery" placeholder="Поиск..."/>
      <div class="appButtons">
        <my-button class="success btnSizeM" style="max-width: 10rem;" @click="showDialog">
          Создать пост
        </my-button>
        <my-select 
          v-model="selectedSort"
          :options="sortOptions"
        />
      </div>
      <my-dialog v-model:show="dialogVisible">
        <PostForm @create="createPost" />
      </my-dialog>
      <PostList v-if="isPostsLoading === false" :posts="sortedAndSearchedPosts" @remove="removePost" />
      <div v-else>
        <span class="loader"></span>
      </div>
      <div v-if="isPostsLoading === false" class="pageWrapper">
        <div 
          class="page" 
          :class="{ 'currentPage': page === pageNumber }" 
          :style="{ 
            cursor: page !== pageNumber ? 'pointer' : 'default',
            pointerEvents: page === pageNumber ? 'none' : '',
          }" 
          v-for="pageNumber in totalPage" 
          :key="pageNumber"
          @click="changePage(pageNumber)"
        >
          {{ pageNumber }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import axios from '../node_modules/axios'

export default {
  components: {
    PostList,
    PostForm
  },
  data() {
    return {
      posts: [],
      // posts: [
      //   { id: 1, title: "JavaScript", body: "Описание поста" },
      //   { id: 2, title: "JavaScript 2", body: "Описание поста" },
      //   { id: 3, title: "JavaScript 3", body: "Описание поста" },
      //   { id: 4, title: "JavaScript 4", body: "Описание поста" },
      // ],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: "",
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По описанию'},
      ],
      searchQuery: '',
      page: 1,
      limit: 10,
      totalPage: 0,
    };
  },
  methods: {
    createPost(post, second, third) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter(currentPost => currentPost.id !== post.id)
    },
    showDialog() {
      this.dialogVisible = true;
    },
    changePage(pageNumber){
      this.page = pageNumber;
    },
    async fetchPosts() {
      try {
        this.isPostsLoading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params:{
            _page: this.page,
            _limit: this.limit,
          }
        });
        this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit);
        this.posts = response.data;
      } catch (error) {
        alert(error)
      }
      finally {
        this.isPostsLoading = false;
      }
    },
  },
  mounted() { // выполнится один ращ в компоненте после того, как компонент был добавлен в DOM.
    this.fetchPosts();
  },
  computed:{
    sortedPosts(){ // сортировка по алфавиту
      return [...this.posts].sort((post1, post2) =>  
        post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedAndSearchedPosts(){ // поиск через фильтер
      return this.sortedPosts.filter((currPost) => 
        currPost.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    },
  },
  watch:{ // свойство которое содержит методы наблюдателей за переменными
    // selectedSort(newValue){
    //   this.posts.sort((post1, post2) => post1[newValue]?.localeCompare(post2[newValue]));
    // },
    page(){
      this.fetchPosts()
    }
  }
};

</script>

<!-- свойство scoped применить стили только к выбранному компоненту -->
<style scoped>
.app {
  padding: 1rem;
}

.containerPost {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.appButtons{
  display: flex;
  justify-content: space-between;
}

.pageWrapper{
  display: flex;
  margin: 1rem 0;
  gap: .5rem;
}
.page{
  color: var(--text-color);
  border: 1px solid var(--green);
  padding: .6rem .9rem;
  border-radius: 8px;
  transition: background-color 0.25s ease-in-out, color 0.35s ease-in-out;
}

.page:hover{
  color: var(--text-color-hover);
  background-color: var(--green);
}

.cursorPage{
  cursor: pointer;
}

.currentPage{
  color: var(--text-color-hover);
  border: 1px solid var(--green);
  padding: .6rem .9rem;
  border-radius: 8px;
  background-color: var(--activePage);
}
</style>
