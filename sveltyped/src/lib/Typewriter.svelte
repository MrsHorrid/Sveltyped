<script>
    import { onMount } from 'svelte';

    export let phrases = [];
    export let loop = false;
    export let typeSpeed = 150;

    let currentPhrase = '';
    let currentIndex = 0;
    let phraseIndex = 0;
    let isDeleting = false;

    const getCommonPrefixLength = (str1, str2) => {
        let commonLength = 0;
        while (
            commonLength < str1.length &&
            commonLength < str2.length &&
            str1[commonLength] === str2[commonLength]
        ) {
            commonLength++;
        }
        return commonLength;
    };

    const type = async () => {
        const current = phrases[phraseIndex];
        const next = phrases[(phraseIndex + 1) % phrases.length];
        const commonPrefixLength = getCommonPrefixLength(current, next);

        if (!isDeleting) {
            currentPhrase = current.slice(0, currentIndex + 1);
            currentIndex++;
        } else {
            if (currentIndex > commonPrefixLength) {
                currentPhrase = current.slice(0, currentIndex - 1);
                currentIndex--;
            }
        }

        if (currentIndex === current.length && !isDeleting) {
            await sleep(1000);
            isDeleting = true;
        } else if (currentIndex === commonPrefixLength && isDeleting) {
            isDeleting = false;
            phraseIndex++;
            if (phraseIndex === phrases.length) {
                if (loop) {
                    phraseIndex = 0;
                } else {
                    return;
                }
            }
        }

        await sleep(isDeleting ? typeSpeed / 3 : typeSpeed);
        type();
    };

    const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

    onMount(() => {
        type();
    });
</script>

<span class="typewriter">{currentPhrase}<span class="cursor">|</span></span>

<style>
    .cursor {
        animation: blink 1s infinite;
    }
    @keyframes blink {
        50% {
            opacity: 0;
        }
    }
</style>