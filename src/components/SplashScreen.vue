<template>
  <div :class="{ loader: true, fadeout: !isLoading }"> 
    <img src="../../public/img/brand/VULCANO-ANIMACAO_2.gif" alt="">
  </div>
</template>

<script>
export default {
  name: "SplashScreen",
  props: ["isLoading"]
};
</script>

<style>
.loader {
  background-color: #EFEFEF;
  bottom: 0;

  display: flex;
  align-items: center;
  justify-content: center;
  
  left: 0;
  overflow: hidden;
  position: fixed;
  right: 0;
  top: 0;
  z-index: 100;
}
.loader img{
    height: 500px;
}
@media(max-width: 600px){
    .loader img{
        height: 200px;
    }
}

.fadeout {
  animation: fadeout 2s forwards;
}

@keyframes fadeout {
  to {
    opacity: 0;
    visibility: hidden;
  }
}
</style>
