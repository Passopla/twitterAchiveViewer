<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { createNoise3D } from 'simplex-noise';
  import { cn } from '$lib/utils';

  export let colors: string[] = ["#38bdf8", "#818cf8", "#c084fc", "#e879f9", "#22d3ee"];
  export let waveWidth = 50;
  export let backgroundFill = "black";
  export let blur = 10;
  export let speed: "slow" | "fast" = "fast";
  export let waveOpacity = 0.5;
  export let className = "";
  export let containerClassName = "";

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D | null = null;
  let w = 0;
  let h = 0;
  let nt = 0;
  let animationId = 0;
  let isSafari = false;

  const noise = createNoise3D();

  const getSpeed = () => {
    switch (speed) {
      case "slow":
        return 0.001;
      case "fast":
        return 0.002;
      default:
        return 0.001;
    }
  };

  const drawWave = (n: number) => {
    if (!ctx) return;
    nt += getSpeed();
    for (let i = 0; i < n; i++) {
      ctx.beginPath();
      ctx.lineWidth = waveWidth;
      ctx.strokeStyle = colors[i % colors.length];
      for (let x = 0; x < w; x += 5) {
        const y = noise(x / 800, 0.3 * i, nt) * 100;
        ctx.lineTo(x, y + h * 0.5);
      }
      ctx.stroke();
      ctx.closePath();
    }
  };

  const render = () => {
    if (!ctx) return;
    ctx.fillStyle = backgroundFill;
    ctx.globalAlpha = waveOpacity;
    ctx.fillRect(0, 0, w, h);
    drawWave(5);
    animationId = requestAnimationFrame(render);
  };

  const init = () => {
    if (!canvas) return;
    ctx = canvas.getContext("2d");
    if (!ctx) return;
    
    w = ctx.canvas.width = window.innerWidth;
    h = ctx.canvas.height = window.innerHeight;
    ctx.filter = `blur(${blur}px)`;
    nt = 0;
    window.onresize = function () {
      if (!ctx || !canvas) return;
      w = ctx.canvas.width = window.innerWidth;
      h = ctx.canvas.height = window.innerHeight;
      ctx.filter = `blur(${blur}px)`;
    };
    render();
  };

  onMount(() => {
    init();
    isSafari = typeof window !== "undefined" &&
      navigator.userAgent.includes("Safari") &&
      !navigator.userAgent.includes("Chrome");
  });

  onDestroy(() => {
    if (animationId) {
      cancelAnimationFrame(animationId);
    }
  });
</script>

<div class={cn("h-screen flex flex-col items-center justify-center", containerClassName)}>
  <canvas
    bind:this={canvas}
    class="absolute inset-0 z-0"
    style={isSafari ? `filter: blur(${blur}px)` : ""}
  />
  <div class={cn("relative z-10", className)}>
    <slot />
  </div>
</div> 