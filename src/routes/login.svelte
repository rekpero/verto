<script lang="typescript">

  import { goto } from "@sapper/app";
  import keyfileSVG from "../assets/keyfile.svg";
  import stroke from "../assets/stroke.svg";
  import { fade } from "svelte/transition";
  import { keyfile, loggedIn, address } from "../stores/keyfileStore.js";

  let isDragOver = false;
  let files: FileList = [];
  let client;

  if(process.browser && $loggedIn) goto("/app");

  // let's create a new client
  if(process.browser) {
    // @ts-ignore
    client = new Arweave({
      host: "arweave.net",
      port: 443,
      protocol: "https",
      timeout: 20000,
    });
  }
  
  $: {
    if(process.browser && client !== undefined && files[0] !== null && files[0] !== undefined && files[0].type === "application/json") {
      const reader = new FileReader();
      reader.onload = () => {
        if(typeof reader.result === "string") keyfile.set(reader.result);
        client.wallets.jwkToAddress(JSON.parse(localStorage.getItem("keyfile"))).then(_address => {
          address.set(_address);
          goto("/app");
        });
      }
      reader.readAsText(files[0]);
    }
  }

  // functions for the drag-overlay
  function drop () {
    isDragOver = false;  
  }
  function drag () {
    isDragOver = true;
  } 

</script>

<svelte:head>
  <title>Verto — Sign In</title>
  <meta name="description" content="Token Exchange System for Arweave Profit Sharing Tokens">
  <meta content="Verto" property="og:title">
  <meta content="Token Exchange System for Arweave Profit Sharing Tokens" property="og:description">
  <!--<meta property="og:image" content="https://image">
  <meta name="twitter:card" content="summary_large_image">-->
  <meta name="twitter:title" content="Verto">
  <meta name="twitter:description" content="Token Exchange System for Arweave Profit Sharing Tokens">
  <!--<meta name="twitter:image" content="https://iimage">-->
</svelte:head>

<input type="file" class="FileInput" class:default={!isDragOver} accept=".json,application/json" on:drop={drop} on:dragover={drag} on:dragleave={drop} bind:files>
{#if isDragOver}
  <div class="drag-overlay" in:fade={{ duration: 350 }} out:fade={{ duration: 160 }}>
    <h1>Drop your keyfile here</h1>
  </div>
{/if}
<div class="Login">
  <div class="instructions">
    <div class="content">
      <h1>Sign in to <span><a href="/">Verto</a></span></h1>
      <p>To sign in, just drag and drop your Arweave Keyfile on this page. <br><br>If you don’t yet have a keyfile, you can get one by creating an <a href="https://www.arweave.org/wallet">Arweave Wallet</a>.</p>
      <p class="notice">Your Arweave Keyfile does not leave your system.</p>
    </div>
  </div>
  <div class="arweave-login">
    <h1>Sign in with your Arweave Keyfile</h1>
    <img src={stroke} alt="stroke" class="Stroke" draggable={false}>
    <img src={keyfileSVG} alt="keyfile" class="Keyfile" draggable={false}>
    <p>If you don’t yet have a keyfile, you can get one by creating an <a href="https://www.arweave.org/wallet">Arweave Wallet</a>.</p>
  </div>
</div>

<style lang="sass">

  =fixedFull()
    position: fixed
    top: 0
    bottom: 0
    left: 0
    right: 0
    width: 100vw
    height: 100vh

  .FileInput
    +fixedFull()
    opacity: 0
    z-index: 1000

    @media screen and (max-width: 720px)
      width: 100vw !important
      height: 80vh !important

    &.default
      width: 50vw
      left: unset
      right: 0

  .drag-overlay
    +fixedFull()
    z-index: 900
    background-color: rgba(#000, .67)

    h1
      font-family: "Inter", sans-serif
      font-size: 2.3em
      color: #fff
      font-weight: 400
      position: absolute
      top: 50%
      left: 50%
      transform: translate(-50%, -50%)

  .Login
    height: 100vh
    background-color: #000
    display: flex

    @media screen and (max-width: 720px)
      display: block

    .instructions, .arweave-login
      position: relative
      width: 50vw
      height: 100%

      @media screen and (max-width: 720px)
        width: 100vw
        height: 100vh

      .content
        position: absolute
        width: 80%

    .instructions
      background-color: #121212

      @media screen and (max-width: 720px)
        display: none

      .content
        top: 50%
        left: 50%
        transform: translate(-50%, -50%)

        h1
          font-family: "Inter", sans-serif
          font-weight: 600
          color: #fff
          text-align: center
          font-size: 2.5em
          margin-bottom: 1.8em

          span
            background: -webkit-linear-gradient(#9300B8, #C54DFD, #E1A1FF)
            -webkit-background-clip: text
            -webkit-text-fill-color: transparent

            a
              text-decoration: none

        p
          font-size: 1.4em
          line-height: 2rem
          text-align: justify
          color: #fff

        p.notice
          font-size: .75em
          color: #828282
          margin-top: 4em
          text-align: center

    .arweave-login
      .Keyfile
        position: absolute
        width: 5em
        top: 50%
        left: 50%
        transform: translate(-50%, -50%)

      .Stroke
        position: absolute
        width: 30vw
        top: 50%
        left: 50%
        transform: translate(-50%, -50%)

        @media screen and (max-width: 720px)
          width: 80vw

      h1, p
        display: none
        margin: none
        position: absolute
        font-size: 1.7em
        color: #fff
        text-align: center
        width: 80vw
        top: 1em
        left: 50%
        transform: translateX(-50%)

        @media screen and (max-width: 720px)
          display: block

      p
        font-size: 1.3em
        top: unset
        bottom: 5vh

</style>
