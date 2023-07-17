<script>
  import Card from "./lib/components/Card.svelte";

  let cardsData = [
    {
      id: 1,
      title: "Card 1",
    },
    // {
    //   id: 2,
    //   title: "Card 2",
    // },
    // {
    //   id: 3,
    //   title: "Card 3",
    // },
    // {
    //   id: 4,
    //   title: "Card 4",
    // },
  ];
</script>

<button
  on:click={() =>
    (cardsData = [
      ...cardsData,
      { id: cardsData.length + 1, title: `Card ${cardsData.length + 1}` },
    ])}>Add Card</button
>


<div class="wrapper">
  {#each cardsData as cardData (cardData.id)}
    <Card {cardData} totalCardCount={cardsData.length}  />
  {/each}
</div>

<style>
  .wrapper {
    display: flex;
  }
</style>
