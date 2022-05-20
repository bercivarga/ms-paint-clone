<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

  let mouseDown = false;

  onMount(() => {
    ctx = canvas.getContext("2d");
    setDimensions();
    window.addEventListener('resize', setDimensions, false);
    
    canvas.addEventListener('mousemove', function(event: MouseEvent) {
      if (!mouseDown) {
        return;
      }
      const {x, y} = getMousePos(canvas, event);
      ctx.fillStyle = "#000000";
      ctx.fillRect (x, y, 4, 4);
    });

    canvas.addEventListener('mousedown', () => mouseDown = true);
    canvas.addEventListener('mouseup', () => mouseDown = false); 
  });

  function setDimensions() {
    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
    ctx.canvas.width  = window.innerWidth;
    ctx.canvas.height = window.innerHeight;
    ctx.putImageData(imageData, 0, 0);
  }

  function getMousePos(c: HTMLCanvasElement, evt: MouseEvent) {
    const rect = c.getBoundingClientRect();
    return {
      x: evt.clientX - rect.left,
      y: evt.clientY - rect.top
    };
  }
</script>

<main>
  <canvas bind:this={canvas} />
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }
</style>
