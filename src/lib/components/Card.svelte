<script>
  import { spring } from "svelte/motion";
  import { fade, fly } from "svelte/transition";

  export let cardData;
  export let totalCardCount = 0;
  export let idx = 0;

  let isDragging = false;
  let cardSavedPos = { x: 0, y: 0 };
  let moveStartPos = { x: 0, y: 0 };

  const maxDeckAngle = 90;
  const deadZoneRange = 150;
  const cardTilt = 120;
  const deckRadius = 250;

  // https://svelte.dev/tutorial/spring
  let cardPosition = spring(
    { x: 0, y: 0, rot: 90 },
    { stiffness: 0.1, damping: 0.25 }
  );

  const getTouchingPos = (e) => {
    e = e.clientX ? e : e.touches[0];
    return { x: e.clientX, y: e.clientY };
  };

  const handleCardTouch = (e) => {
    e.preventDefault();
    isDragging = true;

    // get the position of the mouse
    moveStartPos = getTouchingPos(e);

    // save current card position
    cardSavedPos = {
      x: $cardPosition.x,
      y: $cardPosition.y,
      rot: $cardPosition.rot,
    };
  };

  const handleCardMove = (e) => {
    if (!isDragging) return;

    const pos = getTouchingPos(e);

    // deadzone
    let moveModifier = 1;
    let angle = 0;

    if (
      Math.abs(pos.x - moveStartPos.x) < deadZoneRange &&
      Math.abs(pos.y - moveStartPos.y) < deadZoneRange
    ) {
      moveModifier = 0.3;
      angle = cardSavedPos.rot;
    }

    // move the card
    const deltaX = (pos.x - moveStartPos.x) * moveModifier + cardSavedPos.x;
    const deltaY = (pos.y - moveStartPos.y) * moveModifier + cardSavedPos.y;

    moveCard(deltaX, deltaY, angle);
  };

  const moveCard = (x, y, rot = 0, speed = 0) => {
    cardPosition.set({ x, y, rot }, { soft: speed });
  };

  const tiltAngleDyn = (maxAngle) => {
    const cardsBeforeFullAngle = 10;
    let angle = (maxAngle / cardsBeforeFullAngle) * totalCardCount;
    if (angle > maxAngle) angle = maxAngle;
    return angle;
  };

  const cardBackToDeck = (speed = 3) => {
    const angle = (tiltAngleDyn(maxDeckAngle / 2) * Math.PI) / 180;
    const ratio = (idx) / (totalCardCount - 1); // 0 to 1
    const movedRatio = (ratio - 0.5) * 2; // -1 to 1
    const finalAngle = movedRatio * angle || 0;
    moveCard(
      Math.sin(finalAngle) * deckRadius,
      Math.cos(finalAngle) * deckRadius * -1 + deckRadius / 2,
      (finalAngle * 180 * cardTilt) / maxDeckAngle / Math.PI,
      speed
    );
  };

  const handleMouseUp = (e) => {
    if (!isDragging) return;
    isDragging = false;
    // update
    cardData.id = cardData.id;
  };

  $: {
    cardBackToDeck();
    cardData.id;
  }
</script>

<div
  in:fade
  out:fly={{ y: 100 }}
  class="card"
  on:mousedown={handleCardTouch}
  on:touchstart={handleCardTouch}
  style:--bgColor={`hsl(${$cardPosition.x / 10 + idx * 10}, 50%, 50%)`}
  style:--posX={`${$cardPosition.x}px`}
  style:--posY={`${$cardPosition.y}px`}
  style:--rot={`${$cardPosition.rot}deg`}
  class:dragging={isDragging}
>
  <p>
    {cardData.title}
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
    position: absolute;
    user-select: none;
    margin-inline: -1em;
    aspect-ratio: 5/8;
    border-radius: 1rem;
    width: 10vmin;
    z-index: 1;
    
    transition: box-shadow scale, 0.2s 0.2s, ease-in-out ease-in-out;
    
    background: var(--bgColor);
    /* transition: transform 1s ease-in-out; */
    /* transform-origin: bottom center; */
    /* translate: 0px 100px; */
    translate: var(--posX) var(--posY);
    transform: rotate(var(--rot));
  }

  .card p {
    margin: 0;
    padding: 1rem;
    font-size: 1.5rem;
    font-weight: bold;
    /* text-align: center; */
    color: white;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
      Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
      sans-serif;
  }

  .card.dragging {
    cursor: grabbing;
    /* z-index: 100; */
    box-shadow: 0 0 1rem 0.5rem rgba(0, 0, 0, 0.2);
  }
</style>
