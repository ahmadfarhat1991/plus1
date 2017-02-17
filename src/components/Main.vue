<template>
    <div class="main">
        <h1>Add-1</h1>
        <core/>
        <p>Based on a task from
            <a
                target="_blank"
                href="https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow">
                "Thinking, Fast and Slow" by Daniel Kahneman
            </a>
            :
        </p>
        <p><cite>
            To start, make up several strings of 4 digits, all different,
            and write each string on an index card. Place a blank card on top of the deck.
            The task that you will perform is called Add-1. Here is how it goes:
            Start beating a steady rhythm (or better yet, set a metronome at 1/sec).
            Remove the blank card and read the four digits aloud.
            Wait for two beats, then report a string in which each of the original
            digits is incremented by 1. If the digits on the card are 5294,
            the correct response is 6305. Keeping the rhythm is important.
        </cite></p>
    </div>
</template>

<style>
h1 {
    text-align: center;
}
.main > p {
    width: 50%;
    margin: 10px auto;
}
</style>

<script>
import core from './Core'

export default {
    name: 'main',
    data () { return {} },
    components: {
        core
    }
}
</script>
