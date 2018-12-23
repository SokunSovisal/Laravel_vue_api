<template>
  <div>
    <h1>Articles</h1>
    
    <form @submit.prevent="addArticle" class="mb-5">
      <div class="form-group">
        <label for="">Title</label>
        <input type="text" class="form-control" placeholder="title" v-model="article.title" />
      </div>
      <div class="form-group">
        <label for="">Body</label>
        <textarea class="form-control" placeholder="body" v-model="article.body"></textarea>
      </div>
      <div class="form-group">
        <div class="float-right">
          <button class="btn btn-success">Save</button>
          <button class="btn btn-danger">Clear</button>
        </div>
      </div>
    </form>

    <nav aria-label="Page navigation mb-3">
      <ul class="pagination">
        <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
        <li class="page-item disabled"><a class="page-link" href="#">Page {{pagination.current_page_url}} of {{pagination.last_page_url}}</a></li>
        <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a></li>
      </ul>
    </nav>
    <div class="row">
      <div class="col-sm-4" v-for="article in articles" v-bind:key="article.id">
        <div class="card mb-4">
          <div class="card-body">
            <h5 class="card-title" style="min-height: 40px; overflow:hidden;">{{ article.title }}</h5>
            <p class="card-text" style="min-height: 130px; overflow:hidden;">{{ article.body }}</p>
            <div class="btn-section float-right">
              <button type="button" class="btn btn-warning" @click="editArticle(article)"><i class="fa fa-pencil-alt"></i> Edit</button>
              <button type="button" class="btn btn-danger" @click="deleteArticle(article.id)"><i class="fa fa-trash-alt"></i> Delete</button>
            </div>
          </div>
        </div>
       </div>
    </div>
  </div>
</template>

<script>
  export default {
    data(){
      return{
        articles: [],
        article: {
          id: '',
          title: '',
          body: ''
        },
        article_id: '',
        pagination: {},
        edit: false
      }
    },
    created(){
      this.fetchArticles();
    },
    methods: {
      fetchArticles(page_url){
        let vm = this;
        page_url = page_url || '/api/articles'
        fetch(page_url)
          .then(res => res.json())
          .then(res => {
            this.articles = res.data;
            vm.makePagination(res.meta, res.links);
          })
          .catch(err => console.log(err));
      },
      makePagination(meta, links){
        let pagination = {
          current_page_url: meta.current_page,
          last_page_url: meta.last_page,
          next_page_url: links.next,
          prev_page_url: links.prev
        }
        this.pagination = pagination;
      },
      deleteArticle(id){
        if(confirm('Are you sure?')){
          fetch(`api/article/${id}`, {
            method: 'delete'
          })
          .then(res => res.json())
          .then(data => {
            alert('Article Removed');
            this.fetchArticles();
          })
          .catch(err => console.log(err));
        }
      },
      editArticle(article){
        this.edit = true;
        this.article.id = article.id;
        this.article.article_id = article.id;
        this.article.title = article.title;
        this.article.body = article.body;
      },
      addArticle(id){
        if(this.edit==false){
          // add
          fetch('api/article', {
            method: 'post',
            body: JSON.stringify(this.article),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then(res => res.json())
          .then(data => {
            this.article.title = '';
            this.article.body = '';
            alert('Aticle Added');
            this.fetchArticles();
          })
          .catch(err => console.log(err));
        }else{
          // update
          fetch('api/article', {
            method: 'put',
            body: JSON.stringify(this.article),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then(res => res.json())
          .then(data => {
            this.article.title = '';
            this.article.body = '';
            alert('Aticle updated');
            this.fetchArticles();
          })
          .catch(err => console.log(err));
        }
      }
    }
  }
</script>
