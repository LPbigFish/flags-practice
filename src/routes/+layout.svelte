<script lang="ts">
	import { getContext, onMount, setContext } from "svelte";
    import "../app.css";
	import { writable, type Writable } from "svelte/store";
    import * as unicodeEmoji from 'unicode-emoji';

    let tries: number = 0;
    let correct_tries: number = 0;
    let flag_emoji = "";

    const stats = writable(tries + " " + correct_tries);
    setContext("stats", stats);

    let split: Writable<string> = getContext("stats");
    
    split.subscribe((value: string) => {
        let split = value.split(" ");
        tries = parseInt(split[0]);
        correct_tries = parseInt(split[1]);
    });

onMount(() => {
    const category = 'flags';
    let array = unicodeEmoji.getEmojis({version: ['15.0']});
    array = array.filter(function (el: any) {
        return el.category == category;
    });
    let random = Math.floor(Math.random() * array.length);
    flag_emoji = array[random].emoji;
});

</script>
<svelte:head>
    <title>{ flag_emoji } Flags of the World { flag_emoji }</title>
</svelte:head>
<nav class="flex py-2 px-2 rounded-b-xl bg-indigo-500 shadow-2xl transition-all sm:h-21 h-24">
    <p class="my-auto sm:ml-4 ml-2 w-16 sm:w-32 text-sm sm:text-lg sm:py-3 sm:hover:bg-blue-800 sm:hover:rounded-2xl duration-300 ease-in-out text-center sm:px-2 sm:-mr-2 text-white rounded-2xl bg-indigo-600">Tries: { tries } Correct: { correct_tries }</p>
    <a href="/" class="sm:text-4xl text-base my-auto text-center outline-offset-1 outline-4 grow font-bold font-sans w-fit mx-6 bg-blue-950 rounded-2xl 
    text-white py-3 sm:hover:text-5xl sm:hover:bg-blue-600 sm:hover:rounded-3xl sm:hover:py-3.5 ease-in-out duration-300 delay-75 sm:mx-auto px-3 sm:max-w-sm sm:hover:max-w-lg">Flags of the World</a>
    <a class="my-auto sm:mr-4 text-base sm:text-lg sm:py-3 sm:hover:bg-blue-800 sm:hover:rounded-2xl duration-300 ease-in-out sm:ml-19 text-center ml-1 mr-2 px-3 text-white rounded-2xl bg-indigo-600" target="_blank" href="https://github.com/LPbigFish/">Github</a>
</nav>
<slot>
    // content
</slot>