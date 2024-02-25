<!-- simple counter component -->
<script>
  let count = 0

  function handleClick() {
    count += 1
  }
</script>

<div class="p-5">
  <button class="p-4 bg-red-500" on:click={handleClick}>{count}</button>
</div>
