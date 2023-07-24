<script>
  import { cards } from "$src/stores.js";


  const generateId = () => {
    // generate a random id
    let id = Math.floor(Math.random() * 10000000000000000);
    // check if id already exists
    if ($cards.find((card) => card.id === id)) {
      // if it does, generate a new one
      id = generateId();
    }
    return id;
  }

  const addCard = () => {
    cards.update((cards) => [
      ...cards,
      // { id: cards.length + 1, title: `Card ${cards.length + 1}` },
      { 
        id: generateId(), 
        // title: `Card ${cards.length + 1}` 
        title: cards.length + 1 + ""
      },
    ]);
  };

  const removeCard = () => {
    cards.update((cards) => cards.slice(0, cards.length - 1));
  };
</script>

<button on:click={addCard}>add</button>
<button on:click={removeCard}>remove</button>

<button on:click={() => cards.update((cards) => cards.reverse())}>
  reverse
</button>

<style>
</style>
