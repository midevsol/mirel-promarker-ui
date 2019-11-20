<template>
  <!--
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
  -->
  <header>
    <b-navbar type="light" variant="faded">
      <b-navbar-brand href="#">
        mirelplatform
      </b-navbar-brand>
      <b-navbar-toggle target="nav-collapse" />
      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item to="/">
            Top
          </b-nav-item>
          <b-nav-item to="/mste">
            MSTE
          </b-nav-item>
        </b-navbar-nav>
      </b-collapse>

      <b-navbar-nav class="ml-auto">
        <b-nav-form>
          <b-form-input size="sm" class="mr-sm-2" placeholder="Command Palatte" />
          <b-button size="sm" class="my-2 my-sm-0" type="submit" hidden />
        </b-nav-form>

        <b-nav-item-dropdown right>
          <template v-slot:button-content>
            <span>Account</span>
          </template>
          <b-dropdown-item to="/signin">
            Sign In
          </b-dropdown-item>
        </b-nav-item-dropdown>
      </b-navbar-nav>
    </b-navbar>
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
