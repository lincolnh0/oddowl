<template>
  <main class="container">
    <h1>Getting Started</h1>
    <p>
      Create your own API over at
      <a href="https://the-odds-api.com">The Odds API</a> website and register
      an API key by signing up.
    </p>
    <p>
      Copy the API key you receive from your email and navigate to the
      <RouterLink to="/">homepage</RouterLink> and put it in the textbox.
    </p>

    <p>
      Once entered, the list should refresh and supply you with a list of all
      sports. Clicking on each sports will then consume your API quota by
      displaying a list of all odds by the bookmaker.
    </p>

    <p>
      Usage is calculated when you click on a sports tab as it will then
      retrieve all possible odds for events of that sport. A quota count will
      help you keep track of your API usage. Once the quota reaches 0, you will
      need to pay to use their API at a higher rate. In all pricing tiers, the
      quota resets every month.
    </p>
  </main>
</template>

<script lang="ts">
export default {
  name: "GettingStartedView",
};
</script>

<style scoped>
p {
  line-height: 2rem;
  margin-bottom: 16px;
}
</style>
