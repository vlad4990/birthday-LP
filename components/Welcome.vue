<script setup lang="ts">
</script>

<template>
  <div class="welcome">
    <NuxtImg src="/assets/img/welcome.png" format="webp" height="356" width="346"/>
    <h1 class="welcome__title">Привет, друг!</h1>
    <div class="welcome__description">приглашаю тебя на мой,<br/>
      не побоюсь этого слова, <span>юбилей.</span></div>
  </div>
</template>

<style scoped lang="scss">
.welcome {
  margin-top: 47px;

  &__image {
    height: 356px;
    background-image: url("/assets/img/welcome.webp");
    background-size: contain;
    background-repeat: no-repeat;
    background-position: left;
  }

  &__title {
    font-size: 30px;
    line-height: 100%;
    text-align: center;
    vertical-align: middle;
    margin-top: 26px;
    font-family: Furore, serif;
  }

  &__description {
    font-size: 20px;
    line-height: 110%;
    text-align: center;
    margin-top: 20px;

    span {
      font-family: Furore, serif;
    }
  }

}
</style>
