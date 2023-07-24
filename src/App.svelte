<script>
  import Card from "$lib/components/Card.svelte";
  import CardControls from "$lib/components/CardControls.svelte";
  
  import { cards } from "./stores";
</script>



<div class="card-wrapper">
  {#each $cards as cardData, i (cardData.id)}
    <Card {cardData} idx={i} totalCardCount={$cards.length} />
  {/each}
</div>
<CardControls />

<style>
  .card-wrapper {
    /* position: relative; */
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>


