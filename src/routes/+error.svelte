<script>
  import { page } from '$app/stores';
</script>

<div class="flex flex-col items-center justify-center min-h-screen bg-black text-white p-4">
  <h1 class="text-4xl font-bold mb-4">{$page.status}: {$page.error?.message}</h1>
  <a href="/" class="text-blue-500 hover:text-blue-400">Return Home</a>
</div> 