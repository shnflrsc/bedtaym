<script lang="ts">
    import { GoogleGenerativeAI } from "@google/generative-ai";
    import { story } from "../stores/store";

    const genAI = new GoogleGenerativeAI(import.meta.env.VITE_GEMINI_API_KEY);
    const model = genAI.getGenerativeModel({ model: "gemini-2.0-flash" });

    let isLoading = $state(false);

    let input = $state("");

    async function handleSubmit() {
        isLoading = true;

        let prompt = `create a nonsense, comedic bedtime story that contains these themes: ${input}`;

        const result = await model.generateContent(prompt);
        $story = result.response.text();
    }
</script>

<section class="flex flex-col items-center mt-16">

    <h1 class="text-5xl leading-16">Bedtaym</h1>
    <p>nonsense bedtime stories</p>

</section>

<section class="flex flex-col items-center mt-8">

    <p>what do you want the story to have?</p>
    <textarea class="bg-blue-950/50 rounded-4xl h-60 w-72 mt-2 p-4 outline-none resize-none" bind:value={input}></textarea>
    
    {#if isLoading}
        <span class="loader mt-8"></span>
    {:else}
        <button class="bg-[hsl(225,42%,24%)] hover:bg-[hsl(225,42%,28%)] p-2 rounded-4xl mt-8" onclick={handleSubmit}>tell me a story</button>
    {/if}

</section>

<style>
    .loader {
      width: 30px;
      height: 30px;
      border-radius: 50%;
      position: relative;
      animation: rotate 1s linear infinite
    }
    .loader::before {
      content: "";
      box-sizing: border-box;
      position: absolute;
      inset: 0px;
      border-radius: 50%;
      border: 3px solid #FFF;
      animation: prixClipFix 2s linear infinite ;
    }

    @keyframes rotate {
      100%   {transform: rotate(360deg)}
    }

    @keyframes prixClipFix {
        0%   {clip-path:polygon(50% 50%,0 0,0 0,0 0,0 0,0 0)}
        25%  {clip-path:polygon(50% 50%,0 0,100% 0,100% 0,100% 0,100% 0)}
        50%  {clip-path:polygon(50% 50%,0 0,100% 0,100% 100%,100% 100%,100% 100%)}
        75%  {clip-path:polygon(50% 50%,0 0,100% 0,100% 100%,0 100%,0 100%)}
        100% {clip-path:polygon(50% 50%,0 0,100% 0,100% 100%,0 100%,0 0)}
    }
</style>