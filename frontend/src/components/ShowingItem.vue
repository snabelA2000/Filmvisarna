<template>
   <div>
      <h3>{{ movieTitle }}</h3>
      <router-link :to="'/chosen-movie/' + movieTitleForLink">
         <img class="moviePoster" :src="moviePoster" />
      </router-link>
   </div>
</template>

<script>
export default {
   props: ["moviePoster", "movieTitle"],
   computed: {
      movieTitleForLink() {
         return this.movieTitle.replace(/ /g, "-");
      },
   },
};
</script>

<style scoped>
h3 {
   /* color: wheat; */
   padding: 14px 16px;
}
.moviePoster {
   width: 13vw;
   transition: 200ms;
   border: solid;
   border-color: transparent;
   border-radius: 10px;
  
}
.moviePoster:hover{
   transform:scale(1.05);
   

}
</style>