<template>
    <div class="container">
      <h1>Articles</h1>
      <form @submit.prevent="addArticle()" class="mb-3">
        <div class="form-group">
        <input type="text" class="form-control" placeholder="Title"
        v-model="article.title">
        </div>
        <div class="form-group">
        <textarea class="form-control" placeholder="Title"
        v-model="article.body"></textarea>
        </div>
        <button type="submit" class="btn btn-primary btn-block">Save</button>

      </form>
      <br>
        <nav aria-label="Page navigation example">
          <ul class="pagination">
            <li v-bind:class="[{disabled:!pagination.prev_page_url}]" class="page-item">
            <a class="page-link" href="#" @click="fetchArticle(pagination.prev_page_url)">Previous</a>
            </li>

            <li class="page-item disabled">
            <a class="page-link text-dark" href="#" @click="fetchArticle(pagination.prev_page_url)">
            Page {{pagination.current_page}} of {{pagination.last_page}}</a>
            </li>


            <li v-bind:class="[{disabled:!pagination.next_page_url}]" class="page-item">
            <a class="page-link" href="#" @click="fetchArticle(pagination.next_page_url)">Next</a></li>
          </ul>
      </nav>

      <div class="card card-body mb-2" v-for="article in articles" v-bind:key="article.id">
       <h3>{{article.title}}</h3>
       <p>{{article.body}}</p>
       <hr>
       <button class="btn btn-success" @click="editArticle(article)">Edit</button>
       <br>
       <button class="btn btn-danger" @click="deleteArticle(article.id)">Delete</button>
      </div>
    </div>
</template>


<script>
export default {
 data(){
  return{
      articles:[],
  article:{
     id:'',
     title:'',
     body:''
  },
  article_id:'',
  pagination:{},
  edit:false
  }
 },
  created(){
   this.fetchArticle();
  },
  methods:{
  fetchArticle(page_url){
   let vm = this;
    page_url = page_url || '/api/articles'
     fetch(page_url)
      .then(res => res.json())
      .then(res => {
        this.articles = res.data;
        vm.makePagination(res.meta,res.links);

      })

  },
  makePagination(meta,links){
    let pagination = {
      current_page:meta.current_page,
      last_page:meta.last_page,
      next_page_url:links.next,
      prev_page_url:links.prev
    }
    this.pagination = pagination;
  },
  deleteArticle(id){
   if(confirm('Are you sure?')){
   fetch(`api/article/${id}`,{
   method:'delete'
   })
   .then(res => res.json())
   .then(data =>{
   alert('Article Deleted');
   this.fetchArticle();
   })

   }
  },
  addArticle(){
    if(this.edit === false){
    //adding
    fetch('api/article',{
    method:'post',
    body:JSON.stringify(this.article),
    headers:{
      'content-type':'application/json'
    }

    })
    .then(res => res.json())
    .then(data => {
      this.article.title = '';
      this.article.body='';
      alert('Article Added');
      this.fetchArticle();
    })
    }else{
      //update
      fetch('api/article',{
      method:'put',
      body:JSON.stringify(this.article),
      headers:{
        'content-type':'application/json'
      }

      })
      .then(res => res.json())
      .then(data => {
        this.article.title = '';
        this.article.body='';
        alert('Article Updated');
        this.fetchArticle();
      })
    }
  },
  editArticle(article){
  this.edit = true;
  this.article.id = article.id;
  this.article.article_id = article.id;
  this.article.title = article.title;
  this.article.body = article.body;

  }


  }
}

</script>
