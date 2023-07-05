<script lang="ts">
	import { getContext, onMount } from "svelte";
	import type { Writable } from "svelte/store";
    
    let parsedList: [string, string][] = [];
    let countries: [string, string][] = [];
    let guesses: string[] = [];
    let tries: number = 0;
    let correct_tries: number = 0;
    
    const stats: Writable<string> = getContext("stats");

    let flag: string[] = [];
    
    onMount(async () => {
        load_flags();
    });

    async function load_flags() {
        let flags = await fetch('https://flagcdn.com/en/codes.json');
        flags = await flags.json();

        parsedList = Object.entries(flags);

        countries = parsedList.filter(function (el: (string | any[])[]) {
        return el[0].length <= 2;
        });
        countries.sort(() => Math.random() - 0.5);

        guesses = [countries[0][1], countries[1][1], countries[2][1], countries[3][1]];
        guesses.sort(() => Math.random() - 0.5);

        flag = [countries[0][1] , get_flag(countries[0][0])];

        console.log(flag);
    }

    const get_flag = (country: string) => {
        return 'https://cdn.jsdelivr.net/npm/country-flag-emoji-json@2.0.0/dist/images/' + country.toUpperCase() + '.svg';
    }
    
    
    let correct : boolean = false;
    let tried: boolean = false;

    function check_guess(params: number) {
        console.log(params);
        if (tried == false) {
            if (guesses[params] == flag[0] && correct == false) {
                document.getElementById("btn" + guesses.indexOf(flag[0], 0))?.classList.remove("bg-orange-500");
                document.getElementById("btn" + guesses.indexOf(flag[0], 0))?.classList.add("bg-green-600");
                correct = true;
                correct_tries++;
            } else if (correct === false) {
                document.getElementById("btn" + params)?.classList.add("bg-red-500");
                document.getElementById("btn" + guesses.indexOf(flag[0], 0))?.classList.remove("bg-orange-500");
                document.getElementById("btn" + guesses.indexOf(flag[0], 0))?.classList.add("bg-green-600");
            }
            tried = true;

            setTimeout(() => {
                if (correct === false) {
                    document.getElementById("btn" + params)?.classList.remove("bg-red-500");
                }
                document.getElementById("btn" + guesses.indexOf(flag[0], 0))?.classList.remove("bg-green-600");
                document.getElementById("btn" + guesses.indexOf(flag[0], 0))?.classList.add("bg-orange-500");

                tried = false;
                correct = false;

                tries++;

                stats.set(tries + " " + correct_tries);
                                
                load_flags();
            }, 2400);
        }
    }
</script>

<div class="sm:w-[calc(140*4px)] h-[calc(128*4px)] bg-orange-300 mx-auto mt-8 sm:my-12 sm:h-[calc(200*4px)] rounded-3xl shadow-2xl border-2 border-orange-500 flex flex-col">
    <div class="mx-auto sm:max-w-xl sm:w-[calc(110*4px)] max-w-xs top-4 h-1/2 overflow-hidden">
        <img id="flag" src="{ flag[1] }" alt="{ "redirected" }" class="w-full -mt-2" />
    </div>
    <div class="flex flex-col mx-auto min-w-full -mt-2 sm:-mt-4">
        {#each guesses as guess, i }
            <button on:click={() => {check_guess(i)}} id="btn{ i }" class="max-w-xs w-4/5 my-0.5 h-14 bg-orange-500 rounded-3xl border-4 border-orange-950 mx-auto duration-200 ease-in-out sm:max-w-lg sm:h-20 sm:text-3xl font-bold"> { guess }</button>
        {/each}
    </div>
</div>
