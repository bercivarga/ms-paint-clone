<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

  let mouseDown = false;
  let color = "#000000";
  let lineWidth = 10;

  onMount(() => {
    ctx = canvas.getContext("2d");
    setDimensions();
    ctx.fillStyle = "#FFFFFF";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    window.addEventListener("resize", setDimensions, false);
  });

  let drawing = false;

  function handleMouseMove(event: MouseEvent) {
    if (!mouseDown) {
      if (drawing) {
        drawing = false;
      }
      return;
    }
    
    const {x, y} = getMousePos(canvas, event);

    if (!drawing) {
      ctx.strokeStyle = color;
      ctx.lineWidth = lineWidth;
      ctx.beginPath();
      ctx.moveTo(x, y);
    }

    ctx.lineTo(x, y);
    ctx.stroke();

    drawing = true;
  }

  function setDimensions() {
    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
    ctx.canvas.width  = window.innerWidth;
    ctx.canvas.height = window.innerHeight;
    ctx.putImageData(imageData, 0, 0);
  }

  function getMousePos(c: HTMLCanvasElement, evt: MouseEvent) {
    const rect = c.getBoundingClientRect();
    return {
      x: evt.pageX - rect.left,
      y: evt.pageY - rect.top
    };
  }

  function saveImage() {
    const link = document.createElement('a');
    link.setAttribute("download", "magnificent_art.png");
    link.setAttribute("href",  canvas.toDataURL("image/png").replace("image/png", "image/octet-stream"));
    link.click();
    link.remove();
  }
</script>

<main>
  <div class="controls-container">
    <div class="input-control-container">
      <input type="color" bind:value={color} title={color} />
      <p>Color: {color}</p>
    </div>
    <div class="input-control-container">
      <input
        type="range"
        min={1}
        max={100}
        bind:value={lineWidth}
        title={String(lineWidth)}
      />
      <p>Line weight: {lineWidth}</p>
    </div>
    <button
      type="button"
      class="input-control-container control-btn"
      on:click="{() => color = "#FFFFFF"}"
    >
      Erase
    </button>
    <button
      type="button"
      class="input-control-container control-btn"
      on:click="{() => ctx.clearRect(0, 0, canvas.width, canvas.height)}"
    >
      Clear
    </button>
    <button
      type="button"
      class="input-control-container control-btn"
      on:click={saveImage}
    >
    Save
  </button>
  </div>
  
  <canvas
    bind:this={canvas}
    on:mousemove={handleMouseMove}
    on:mousedown="{() => mouseDown = true}"
    on:mouseup="{() => mouseDown = false}"
  />
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  input, button {
    cursor: pointer;
  }

  .input-control-container {
    display: flex;
    flex-direction: row;
    gap: 8px;
    align-items: center;
    background: none;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 1px 4px;
    padding: 8px 16px;
    border-radius: 8px;
    border: 1.5px solid white;
    font-size: 16px;
  }

  .control-btn:hover {
    background: gainsboro;
  }

  .controls-container {
    position: absolute;
    top: 24px;
    left: 24px;
    display: flex;
    flex-direction: row;
    gap: 8px;
    align-items: stretch;
    height: fit-content;
  }
</style>
