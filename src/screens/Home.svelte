<script>
  import { createEventDispatcher, onMount } from "svelte";
  import api from "../api";

  const dispatch = createEventDispatcher();

  export let error;
  let disabled = false;
  let name, url;
  const hasLocalKey = process.env.SVELTE_APP_DAILY_API_KEY;

  onMount(() => {
    const storedUrl = localStorage?.getItem("svelte-prebuilt-url");
    const storedName = localStorage?.getItem("svelte-prebuilt-name");
    if (storedUrl) {
      url = storedUrl;
    }
    if (storedName) {
      name = storedName;
    }

    // only use the create room button on deployed sites
    disabled =
      window.location.origin === "http://localhost:5000" && !hasLocalKey;
  });

  const goToCall = (e) => {
    e.preventDefault();
    dispatch("submit", {
      name,
      url,
    });
  };

  const createNewRoom = async () => {
    error = null;
    const res = await api.createRoom();

    if (res?.url) {
      url = res.url;
      dispatch("submit", {
        name: "",
        url,
      });
    } else {
      console.error(res);
      error = "Room could not be created. Please check your local config.";
    }
  };
</script>

<div class="home-screen">
  <h2>Daily Prebuilt Svelte demo</h2>
  <p>Start demo with a new unique room or paste in your own room URL</p>
  <button on:click={createNewRoom} {disabled}> Create room and start </button>
  {#if error}
    <p class="error">{error}</p>
  {/if}
  <p>or</p>
  <form on:submit={goToCall}>
    <label for="name">Name</label>
    <input id="name" type="text" required bind:value={name} />
    <label for="url">Daily URL</label>
    <input
      id="url"
      type="text"
      required
      bind:value={url}
      placeholder="https://your-domain.daily.co/room-name"
    />
    <input type="submit" value="Join call" />
  </form>
</div>

<style>
  .home-screen {
    padding: 0 1rem;
    max-width: 600px;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: auto;
    justify-content: center;
  }
  h2 {
    font-size: 32px;
    margin: 0;
    text-align: center;
  }
  p {
    font-size: 16px;
    margin: 16px;
    text-align: center;
    
  }
  label {
    display: block;
    color: var(--dark-grey);
    font-size: 12px;
    margin-bottom: 4px;
    margin-top: 8px;
  }
  input {
    border: none;
    background-color: var(--white);
    border: none;
    border-radius: 8px;
    padding: 8px;
    width: calc(280px - 16px); /* subtract extra input padding from width */
    border: 1px solid var(--grey);
  }
  input[type="submit"] {
    display: block;
    background-color: var(--white);
    border: none;
    border-radius: 8px;
    width: 280px;
    padding: 8px 0;
    font-size: 12px;
    font-weight: 600;
    margin-top: 16px;
    border: 1px solid var(--grey);
    cursor: pointer;
  }
  button {
    background-color: var(--turquoise);
    border: none;
    border-radius: 8px;
    width: 280px;
    padding: 8px 0;
    font-size: 12px;
    font-weight: 600;
    cursor: pointer;
  }
  button:disabled {
    background-color: var(--grey);
    color: var(--dark-grey);
    cursor: not-allowed;
  }
  .error {
    color: var(--red-dark);
  }
</style>
