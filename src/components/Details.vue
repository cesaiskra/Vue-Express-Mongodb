<template lang="html">
  <div>
    <div class="preloader-wrapper big active app-loading" v-if="loading">
      <div class="spinner-layer spinner-blue">
        <div class="circle-clipper left">
          <div class="circle"></div>
        </div><div class="gap-patch">
          <div class="circle"></div>
        </div><div class="circle-clipper right">
          <div class="circle"></div>
        </div>
      </div>
    </div>
    <div class="detail" v-else>
      <div class="detail-left">
        <img class="movie-poster"/>
      </div>
      <div class="detail-right">
        <p class="movie-title">{{ movie.title }} <span>{{ movie.original_title}}</span></p>
        <p class="movie-akas">别名:<span class="movie-aka" v-for="aka of movie.aka">{{aka}}</span></p>
        <p class="movie-genres">
          {{movie.countries.join('')}} ({{movie.year}})
          <span  class="movie-genre" v-for="genre of movie.genres">{{genre}}</span>
          评分: {{movie.rating.average}}
        </p>
        <p class="movie-directors">导演:&nbsp;<a :href="dir.alt" v-for="dir of movie.directors">{{dir.name}}</a></p>
        <p class="movie-actors">
          演员:&nbsp;<a v-for="actor of movie.casts" class="movie-actor" :href="actor.alt">{{actor.name}}</a>
        </p>
        <p class="movie-summary">{{movie.summary}}</p>
        <a @click="goBack" class="waves-effect waves-light btn"><i class="material-icons left">keyboard_backspace</i>返回</a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  created () {
    this.getMovie(this.$route.params.title)
    document.title = this.$route.name
  },
  data () {
    return {
      movie: {},
      loading: true
    }
  },
  methods: {
    goBack () {
      this.$router.go(-1)
    },
    getMovie (title) {
      let searchUrl = 'https://bird.ioliu.cn/v1/?url=http://api.douban.com/v2/movie/search?q='
      this.$http.get(`${searchUrl}${title}`)
        .then(res => {
            let movieUrl = 'https://bird.ioliu.cn/v1/?url=http://api.douban.com/v2/movie/subject'
            let movieId = res.data.subjects[0].id
            if(!!movieId){
              this.$http.get(`${movieUrl}/${movieId}`)
              .then(res => {
                console.dir(res.data)
                if (!!res.data) {
                  this.movie = res.data
                  setTimeout(()=>{
                    document.querySelector('.movie-poster').src = this.movie.images.large
                  }, 0)
                  this.loading = false
                }
              })
              .catch(e => console.log(e))
            }
        })
        .catch(e => console.log(e))
    }
  }
}
</script>

<style lang="css" scoped>
a {
  color: #009dd7;
}
.spinner-blue, .spinner-blue-only {
  border-color: #009dd7;
}
.btn {
  color: #fff !important;
  background-color: #009dd7 !important;
}
.loading {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
}
.detail {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  text-align: left;
}
.detail-left {
  padding: 16px;
  margin-left: 80px;
}
.detail-right {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}
.movie-title {
  width: 100%;
  font-size: 24px;
}
.movie-actors {
  width: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
.movie-actor {
  margin-right: 12px;
}
.movie-genres {
  width: 100%;
}
.movie-genre {
  margin-right: 12px;
}
.movie-poster {
  width: 300px;
  height: 450px;
}
.movie-directors {
  width: 100%;
}
.movie-summary {
  letter-spacing: 1px;
  text-indent: 2em;
  line-height: 1.4;
  font-size: 16px;
}
.movie-akas {
  width: 100%;
}
.movie-aka {
  margin-right: 12px;
}
</style>
