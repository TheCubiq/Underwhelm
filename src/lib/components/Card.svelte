<script>
  import { spring } from "svelte/motion";

  export let cardData;
  let isDragging = false;
  let moveStartPos = { x: 0, y: 0 };

  // https://svelte.dev/tutorial/spring
  let cardPosition = spring({ x: 0, y: 0 }, { stiffness: 0.1, damping: 0.25 });

  const getTouchingPos = (e) => {
    e = e.clientX ? e : e.touches[0];
    return { x: e.clientX, y: e.clientY }
  };

  const handleCardTouch = (e) => {
    e.preventDefault();
    isDragging = true;

    // get the position of the mouse
    const pos = getTouchingPos(e);

    moveStartPos = {
      x: pos.x - $cardPosition.x,
      y: pos.y - $cardPosition.y,
    };
    console.log(e, moveStartPos);
  };

  const handleCardMove = (e) => {
    if (!isDragging) return;

    const pos = getTouchingPos(e);

    console.log(e);
    // move the card
    const deltaX = pos.x - moveStartPos.x;
    const deltaY = pos.y - moveStartPos.y;
    moveCard(deltaX, deltaY);
  };

  const moveCard = (x, y, speed = 0) => {
    cardPosition.set({ x: x, y: y }, { soft: speed });
  };

  const handleMouseUp = (e) => {
    if (!isDragging) return;
    isDragging = false;
    moveCard(0, 0, 3);
  };
</script>

<div
  class="card"
  on:mousedown={handleCardTouch}
  on:touchstart={handleCardTouch}
  style:--bgColor={`hsl(${$cardPosition.x / 10 + cardData.id * 5}, 50%, 50%)`}
  style:--posX={`${$cardPosition.x}px`}
  style:--posY={`${$cardPosition.y}px`}
  class:dragging={isDragging}
>
  <p>
    {cardData.id}
  </p>
</div>

<svelte:window
  on:mousemove={handleCardMove}
  on:touchmove={handleCardMove}
  on:mouseup={handleMouseUp}
  on:touchend={handleMouseUp}
/>

<style>
  .card {
    user-select: none;
    margin-inline: -1em;
    aspect-ratio: 5/8;
    border-radius: 1rem;
    width: 10vmin;
    background: var(--bgColor);
    z-index: 1;

    transition: box-shadow 0.2s ease-in-out;

    /* transition: transform 1s ease-in-out; */
    transform-origin: bottom center;
    transform: translate3d(var(--posX), var(--posY), 0);
  }

  .card p {
    margin: 0;
    padding: 1rem;
    font-size: 1.5rem;
    font-weight: bold;
    text-align: center;
    color: white;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
      Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
      sans-serif;
  }

  .card.dragging {
    cursor: grabbing;
    z-index: 100;
    box-shadow: 0 0 1rem 0.5rem rgba(0, 0, 0, 0.2);
  }
</style>
