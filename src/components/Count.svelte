<script lang="ts">
    let count = 0

    function handleClick() {
        count += 1
    }
</script>

<div class="">
    hej

    <button class="p-4 bg-black" on:click={handleClick}>{count}</button>
</div>
