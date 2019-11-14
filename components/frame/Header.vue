<template lang="html">
  <header id="mui-global-wrap">
    <div class="mui-global-logo-wrap" />
    <div>
      <nav id="mui-global-navi">
        <ul>
          <router-link class="mui-global-navi-item" tag="li" to="/" exact>
            <a>TOP</a>
          </router-link>
          <router-link class="mui-global-navi-item" tag="li" to="/mste" exact>
            <a>MSTE</a>
          </router-link>
        </ul>
      </nav>
    </div>
    <div class="mui-global-util-wrap" />
  </header>
</template>

<script>
export default {
  name: 'CommonHeader'
}
</script>

<style lang="css" scoped>
#mui-global-wrap {
  display:flat;
}

#mui-header {
  height:30px;
  display:flat;
  justify-content:space-around;
  margin:0;
}

.mui-global-navi-item {
  width:60px ;
  text-align:center;
  padding:20px;
  margin:0;
}

.mui-global-navi-item a {
  color:white;
}

.mui-global-navi-item:hover {
  background-color:#000000;
  opacity:0.5;
}

#mui-global-navi {
  list-style: none;
  padding: 0;
  background-color: rgba(220, 53, 53, 0.89);
}
#mui-global-navi li {
  display: inline-block;
  padding: 5px;
}

.mui-global-logo-wrap {
  width: 300px;
}

</style>
