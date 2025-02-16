<script lang="ts">
    let { children } = $props();

    import "../app.css";

</script>

<svelte:head>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css2?family=Indie+Flower&display=swap" rel="stylesheet">
</svelte:head>

<main class="font-body text-white">
    {@render children()}
</main>

<style>
    :root {
        background-size: cover;
        background-image: url(/background.png);
    }
</style>