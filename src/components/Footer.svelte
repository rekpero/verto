<script lang="typescript">

  import { fade } from "svelte/transition";
  import { backOut } from "svelte/easing";

  let element, y, windowHeight, shown = false;

  // fade animation
  $: {
    if(element !== undefined) {
      let componentY = element.offsetTop + element.offsetHeight;
      if(componentY <= (y + windowHeight + 120) && !shown) shown = true;
    }
  }

</script>

<svelte:window bind:scrollY={y} bind:innerHeight={windowHeight} />
<div bind:this={element}>
  {#if shown}
    <div class="Footer" in:fade={{ duration: 1100, delay: 411, easing: backOut }}>
      <div class="FooterContent">
        <div>
          <a href="/trade">trade</a>
        </div>
        <div>
          <a href="https://github.com/useverto/verto">code</a>
        </div>
        <div>
          <a href="https://www.arweave.org/">arweave</a>
        </div>
      </div>
    </div>
  {/if}
</div>

<style lang="sass">

  .Footer
    width: 80%
    border-top: 5px solid black
    margin: 0 auto
  
    .FooterContent
      margin: 30px auto
  
      div
        width: 32%
        display: inline-block
        text-align: center
  
        @media screen and (max-width: 720px)
          display: inline-block
          width: 30%
  
        a
          text-decoration: none
          width: 100% !important
          transition: color linear 0.1s
          color: black
        
          &:hover
            transition: color linear 0.1s
            color: #C54DFD

</style>