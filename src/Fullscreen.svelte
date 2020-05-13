<style>
  .isFull {
    width: 100vw;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #262626;
  }
  button {
    color: #000;
    position: absolute;
    right: 20px;
    bottom: 20px;
  }
</style>

<script>
  import { onMount } from 'svelte';

  // define initial component state
  let isFull = false;
  let fsContainer = null;

  // boring plain js fullscreen support stuff below
  const noop = () => {};

  const fullscreenSupport = !!(
    document.fullscreenEnabled ||
    document.webkitFullscreenEnabled ||
    document.mozFullScreenEnabled ||
    document.msFullscreenEnabled ||
    false
  );

  const exitFullscreen = (
    document.exitFullscreen ||
    document.mozCancelFullScreen ||
    document.webkitExitFullscreen ||
    document.msExitFullscreen ||
    noop
  ).bind(document);

  const requestFullscreen = () => {
    const requestFS = (
      fsContainer.requestFullscreen ||
      fsContainer.mozRequestFullScreen ||
      fsContainer.webkitRequestFullscreen ||
      fsContainer.msRequestFullscreen ||
      noop
    ).bind(fsContainer);
    requestFS();
  };

  onMount(() => {
    // Add the icon stylesheet dynamically
    const link = document.createElement('link');
    link.rel = 'stylesheet';
    link.href = 'https://fonts.googleapis.com/icon?family=Material+Icons';
    document.head.appendChild(link);

    // remove the link when component is unmounted
    return () => {
      link.parentNode.removeChild(link);
    };
  });

  // handler for the fullscreen button
  const fsToggle = () => {
    if (!fullscreenSupport) return;

    if (isFull) {
      exitFullscreen();
    } else {
      requestFullscreen(fsContainer);
    }
    isFull = !isFull;
  };

  // the icon name is computed automagically based
  // on the state of the screen
  $: icon = isFull ? 'fullscreen_exit' : 'fullscreen';
</script>

<div class="fs" class:isFull bind:this={fsContainer}>
  <slot {isFull} />
  {#if fullscreenSupport}
    <button on:click={fsToggle}>
      <i class="material-icons md-36">{icon}</i>
    </button>
  {/if}
</div>
