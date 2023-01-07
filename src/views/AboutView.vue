<template>
  <main class="container">
    <h1>About OddOwl</h1>
    <p>
      OddOwl is an independently developed web application that interacts with
      the
      <a href="https://the-odds-api.com/liveapi/guides/v4/#endpoint-3>"
        >Odds API</a
      >. It provides several clear dashboards where you can view a range of odds
      posted by various bookmakers in your regions.
    </p>
    <p>
      The application is not-for-profit and therefore is unable to afford the
      advanced pricing tier on the API service. Instead, it provides an API
      field should you wish to use your own token to interact with the API
      yourself.
    </p>

    <p>Future roadmap</p>
    <ul>
      <li>Tracking other supported sports</li>
      <li>
        Tracking other supported markets such as point spread and over/under
      </li>
    </ul>
  </main>
</template>

<script setup lang="ts"></script>
<style lang="scss" scoped>
p {
  line-height: 2rem;
  margin-bottom: 16px;
}
</style>
