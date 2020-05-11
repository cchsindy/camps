<template>
  <div id="app">
    <img src="@/assets/theatrecamp.jpg" />
    <h1>Theatre Camp 2020 has been cancelled.</h1>
    <p>
      Questions? Contact
      <a href="mailto:theatre@covenantchristian.org">theatre@covenantchristian.org</a>.
    </p>
    <img class="logo" src="@/assets/cchs-logo.svg" alt="CCHS Logo" />
  </div>
</template>

<script>
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
button {
  font-size: 1.5em;
}
img {
  width: 60vw;
}
input {
  font-size: 1.5em;
}
input[type="number"] {
  width: 100px;
}
.card {
  margin-left: 20vw;
  width: 60vw;
}
.flex {
  display: flex;
  justify-content: center;
}
.logo {
  width: 50vw;
}
.message {
  font-size: 1.5em;
  font-weight: bold;
}
.space {
  margin: 4vh 1vw;
}
.spinner {
  width: 20vw;
}
@media only screen and (max-width: 600px) {
  .card {
    margin-left: 0;
    width: auto;
  }
}
</style>
