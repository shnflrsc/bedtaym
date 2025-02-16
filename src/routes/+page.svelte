<script lang="ts">
    import { GoogleGenerativeAI } from "@google/generative-ai";

    const genAI = new GoogleGenerativeAI(import.meta.env.VITE_GEMINI_API_KEY);
    const model = genAI.getGenerativeModel({ model: "gemini-2.0-flash" });

    let isLoading = $state(false);
    let hasStory = $state(false);

    let story = $state(``);
    let title: string | null = $state("");

    let input = $state("");

    function extractFirstQuotedLineLargeString(text: string) {
        let startIndex = text.indexOf('"'); // Find the first opening quote
    
        if (startIndex === -1) {
            return null; // No quotes found
        }
    
        startIndex++; // Move past the opening quote
        let endIndex = text.indexOf('"', startIndex); // Find the next closing quote
    
        if (endIndex === -1) {
            return null; // No closing quote
        }
    
        return text.substring(startIndex, endIndex);
    }

    function removeFirstQuotedText(text: string) {
        const startIndex = text.indexOf('"');

        if (startIndex === -1) {
            return text; // No quotes found, return original string
        }

        const endIndex = text.indexOf('"', startIndex + 1);

        if (endIndex === -1) {
            return text; // No matching closing quote, return original string
        }

        return text.slice(0, startIndex) + text.slice(endIndex + 1);
}

    async function handleSubmit() {
        isLoading = true;
        
        let prompt = `create a nonsense, comedic bedtime story that contains these themes: ${input}. Create a title at the start in quotes. Format it in paragraphs, that is, paragraphs should be spaced two lines each. Don't make an intro, jump straight into the story. Make it unique. Use names unique to the story, no cliche names like 'Bartholomew'.`;

        try {
            const result = await model.generateContent(prompt);
            story = removeFirstQuotedText(result.response.text());

        } catch (error) {
            console.error(error);
            isLoading = false;
        } finally {
            title = extractFirstQuotedLineLargeString(story);
            isLoading = false;
            hasStory = true;
        }
    }

    function handleBack() {
        hasStory = false;
    }
</script>

<svelte:head>
    <title>Bedtaym</title>
</svelte:head>

{#if !hasStory}

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

{:else}

<section class="flex flex-col items-center mt-16 mb-16 mx-8">
    <h1 class="text-3xl text-center">"{title}"</h1>
    <p class="leading-10 mt-16">{story}</p>
    <button class="bg-[hsl(225,42%,24%)] hover:bg-[hsl(225,42%,28%)] p-2 rounded-4xl mt-8" onclick={handleBack}>go back</button>
</section>

{/if}

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