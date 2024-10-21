<script lang="ts">
  import Chat from "./lib/chat.svelte";
  import Login from "./lib/login.svelte";
  import { currentUser, logout, pb } from "./lib/pocketbase";
  // TODO: remove this
  pb.authStore.clear();

  // const isSignedIn: boolean = !!pb.authStore.model;
</script>

<div class="bg-blue-950">
  <!-- App bar on top -->
  <header class="text-white flex flex-row justify-around p-5 bg-black">
    <h1 class="py-2">Spock Chat App 💬</h1>
    {#if $currentUser}
      <button on:click={logout} class="py-2 rounded px-3 bg-gray-500">
        Sign Out
      </button>
    {/if}
  </header>

  <div class="min-h-[90vh] flex flex-col justify-center items-center">
    {#if !$currentUser}
      <Login></Login>
    {:else}
      <Chat></Chat>
    {/if}
  </div>
</div>
