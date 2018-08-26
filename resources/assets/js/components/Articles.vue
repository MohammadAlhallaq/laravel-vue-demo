<template>
    <div>
        <h2>Articles</h2>

    <ul class="pagination">
        <li class="page-item" v-bind:class="[{disabled: !pagination.prev_page}]" v-on:click="fetchArticles(pagination.prev_page)">
        <a class="page-link" href="#" aria-label="Previous">
            <span aria-hidden="true">&laquo;</span>
            <span class="sr-only">Previous</span>
        </a>
        </li>

        <div v-for="n in pagination.last_page">
                <li class="page-item" v-on:click="fetchArticles(pagination.path + '?page=' + n)"><a class="page-link" href="#">{{n}}</a></li>
        </div>
        

        <li class="page-item" v-bind:class="[{disabled: !pagination.next_page}]" v-on:click="fetchArticles(pagination.next_page)">
        <a class="page-link" href="#" aria-label="Next">
            <span aria-hidden="true">&raquo;</span>
            <span class="sr-only">Next</span>
        </a>
        </li>
    </ul>


        <div class="card card-body mb-5" v-for="article in articles.data" v-bind:key="article.id">
            <h3>{{ article.title }}</h3>
            <p>{{ article.body}}</p>
            <hr>
            <div>
                <button class="btn btn-danger col-sm-2" v-on:click="deleteArticle(article)">Delete</button>
                <button class="btn btn-default col-sm-2">Edit</button>
            </div>
            
        </div>
    </div>
</template>




<script>
export default {
    data(){
        return {
            articles: [],

            article: {
                id: '',
                title: '',
                body: ''
            },
            
            article_id: '',
            pagination: {},
            edit: false,
        }
    },


    created(){
        this.fetchArticles();
    },



    methods: {
        fetchArticles(page_url){

            page_url = page_url || 'api/articles';
            axios.get(page_url)
            .then((response) =>{
                this.articles = response.data;
                this.makePagination(response.data.meta, response.data.links)
            })
            .catch(err => console.log(err))
        },
        
        makePagination(meta, links){
            let pagination = {
                current_page: meta.current_page,
                last_page: meta.last_page,
                next_page: links.next,
                prev_page: links.prev,
                path: meta.path
            };

            this.pagination = pagination;
        },


        deleteArticle(article){

            if(confirm('are you suer')){

            axios.delete('api/article/' + article.id)
            .then(res => {

                let index = this.articles.data.indexOf(article);

                if(index > -1){
                this.articles.data.splice(index, 1);
                alert('article has been removed');
                }
            })
            .catch(err => console.log(err));
            }
        }

    }
}
</script>
