<template>
    <div class="overview-item">
        <div class="overview-icon">
            <v-icon
                v-if="type==='success'"
                color="white"
                class="overview-icon"
            >
            mdi-check
            </v-icon>
            <v-icon
                v-if="type==='error'"
                color="white"
                class="overview-icon"
            >
            mdi-close-circle-outline
            </v-icon>
            <v-icon
                
                v-if="type==='warning'"
                color="white"
                class="overview-icon"
            >
            mdi-alert
            </v-icon>
            
        </div>
        <div class="overview-text">
            <h3>
                {{ title }}
            </h3>
            <span class="count">
                {{ value }}
            </span>
        </div>
    </div>
</template>

<script>
  export default {

    props: ['type','title','value','link'],

    name: 'OverviewCard',
  }
</script>

<style scoped>
.overview-icon{
    border-radius: 50%;
    padding: 5px;
}
.overview-text{
    padding: 10px;
}
.overview-text h3{
    font-weight: 500;
    font-size: 0.9rem;
    color: #505050;
}
.overview-text .count{
    font-weight: 900;
    font-size: 1.8em;
}
.overview-item{
    display: flex;
    align-items: center;
    background: #f7f7f7;
    border-radius: 5px;
    margin: 10px;
    padding: 15px;
    cursor: pointer;
    /* transition: all 0.2s ease; */
    /* box-shadow: 5px 10px 10px 5px rgb(29 56 70 / 6%); */
}
.overview-item:hover{
  /* transition: all 0.2s ease;
  box-shadow: 4px 7px 20px 2px rgb(0 0 0 / 10%); */
}
.tag-success{
    background: #E5F8DC;
}
.tag-success span{
    color: #4CAF50;
}
.tag-success .overview-icon{
    background: #67AC5B;
}
.tag-warning{
    background: #FDF1DF;
}
.tag-warning span{
    color: #ffc401;
}
.tag-warning .overview-icon{
    background: #ffc401;
}
.tag-error{
    background: #FBE7E7;
}
.tag-error span{
    color: #ff4b4b;
}
.tag-error .overview-icon{
    background: #ff4b4b;
}
</style>
