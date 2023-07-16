<script>
  export let cardData;
  let isDragging = false;
  let mouseStart = { x: 0, y: 0 };
  let cardPosition = { x: 0, y: 0 };

  const handleMouseDown = (e) => {
    e.preventDefault();
    isDragging = true;
    mouseStart = {
      x: e.clientX - cardPosition.x,
      y: e.clientY - cardPosition.y,
    };
    console.log(e, mouseStart);
  };

  const handleMouseMove = (e) => {
    if (!isDragging) return;
    console.log(e);
    // move the card
    const deltaX = e.clientX - mouseStart.x;
    const deltaY = e.clientY - mouseStart.y;
    moveCard(deltaX, deltaY);
  };

  const moveCard = (x, y) => {
    cardPosition = {
      x: x,
      y: y,
    };
  };

  const handleMouseUp = (e) => {
    if (!isDragging) return;
    isDragging = false;
    // moveCard(0, 0);
  };
</script>

<div
  class="card"
  on:mousedown={handleMouseDown}
  style:--posX={`${cardPosition.x}px`}
  style:--posY={`${cardPosition.y}px`}
  style:--cardTilt={`${cardPosition.x / 10}deg`}
  style:--bgColor={`hsl(${(cardPosition.x / 10) + (cardData.id * 5)}, 50%, 50%)`}
  class:dragging={isDragging}
>
  {cardData.id}
</div>

<svelte:window on:mousemove={handleMouseMove} on:mouseup={handleMouseUp} />

<style>
  .card {
    user-select: none;
    margin-inline: -1em;
    aspect-ratio: 5/8;
    border-radius: 1rem;
    width: 10vmin;
    background: var(--bgColor);
    z-index: 1;

    /* transition: transform 1s ease-in-out; */
    transform-origin: bottom center;
    transform: translate3d(var(--posX), var(--posY), 0) rotate(var(--cardTilt));
  }

  .card.dragging {
    cursor: grabbing;
    z-index: 100;
    box-shadow: 0 0 1rem 0.5rem rgba(0, 0, 0, 0.2);
  }
</style>