<template>

  <div class="card">
    <div class="header"><slot name="header"></slot></div>
    
    <div class="content"><slot></slot></div>
    <div class="button"><slot name="button"></slot></div>

    <div class="more"><slot name="more"></slot></div>

  </div>

</template>

<style scoped lang="scss">
  .card{
    padding: 2rem 2.6rem;
    text-align: center;
    min-width: 300px;
    width: 300px;
    border-radius: 10px;
    background: #fff;
    box-shadow: 2px 4px 8px #ccc;
    justify-self: center;
  }
  .header{
    text-align: center;
    font-weight: 900;
    font-size: 1.4rem;
    margin-bottom: 30px;
  }
  .more{
    margin-top: 20px;
  }
  
</style>
