<script>
  import Call from "./screens/Call.svelte";
  import Header from "./components/Header.svelte";
  import Home from "./screens/Home.svelte";

  let currentScreen = "home"; //  || 'call'
  let error = null;
  let url;
  let userName;

  const handleJoinCall = ({ detail }) => {
    currentScreen = "call";
    // set component vars with form submission values
    url = detail.url;
    userName = detail.name;

    // save in local storage
    localStorage?.setItem("svelte-prebuilt-url", url);
    localStorage?.setItem("svelte-prebuilt-name", userName);

    error = null;
  };
  const handleCallError = ({ detail }) => (error = detail?.error);
  const handleLeaveCall = () => (currentScreen = "home");
</script>

<svelte:head>
  <script crossorigin src="https://unpkg.com/@daily-co/daily-js"></script>
</svelte:head>

<div class="wrapper">
  <Header />
  {#if currentScreen === "home"}
    <Home on:submit={handleJoinCall} {error} />
  {:else}
    <Call
      {userName}
      {url}
      on:left={handleLeaveCall}
      on:error={handleCallError}
    />
  {/if}
</div>

<style>
  .wrapper {
    background-color: var(--grey-lightest);
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    overflow-x: hidden;
  }
</style>
