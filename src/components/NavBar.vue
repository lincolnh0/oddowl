<template>
  <nav>
    <RouterLink to="/" class="branding">
      <img
        alt="Odd Owl logo"
        class="logo"
        src="@/assets/owl.svg"
        width="40"
        height="40"
      />
      <span>OddOwl</span>
    </RouterLink>
    <div class="menus">
      <ul class="left">
        <li><RouterLink to="/getting-started">Getting started</RouterLink></li>
        <li><RouterLink to="/about">About</RouterLink></li>
        <!--        <li><RouterLink to="#">Premium</RouterLink></li>-->
      </ul>
      <ul class="right">
        <!--        <li><RouterLink to="#">Login</RouterLink></li>-->
        <!--        <li><RouterLink to="#">Signup</RouterLink></li>-->
        <!--        <li><RouterLink to="#">My Account</RouterLink></li>-->
      </ul>
    </div>
  </nav>
</template>

<script>
export default {
  name: "NavBar",
};
</script>

<style lang="scss" scoped>
nav {
  display: flex;
  justify-content: space-between;
  margin-bottom: 32px;

  a {
    padding: 8px;
  }
}

.branding {
  flex: 2;
  display: flex;

  span {
    font-size: x-large;
    margin-left: 8px;
  }

  &:hover {
    background-color: transparent;
  }
}

.menus {
  display: flex;
  justify-content: space-between;
  flex: 3;
}

.left {
  list-style: none;
  padding: 0;
  display: flex;
  align-items: center;
}

.right {
  list-style: none;
  padding: 0;
  display: flex;
  align-items: center;
}
</style>
