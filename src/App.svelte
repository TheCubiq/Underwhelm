<script>
  import Card from "./lib/components/Card.svelte";

  let cardsData = [
    {
      id: 1,
      title: "Card 1",
    },
    {
      id: 2,
      title: "Card 2",
    },
    {
      id: 3,
      title: "Card 3",
    },
    {
      id: 4,
      title: "Card 4",
    },
    {
      id: 5,
      title: "Card 4",
    },
    {
      id: 6,
      title: "Card 4",
    },
  ]  

</script>

<div class="wrapper">
  {#each cardsData as cardData (cardData.id)}
    <Card {cardData} />     
  {/each}
  
</div>

<style>
.wrapper {
  /*   overflow: hidden; */
  display: flex;
}



</style>