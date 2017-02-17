<template>
<div id="core">
    <p>State: {{ stateText }}</p>
    <p>Time left [ms]: {{ timeLeft || '0000' }}</p>
    <p>Question: {{ question }}</p>
    <p> Answer:
        <input
            ref="answerInput"
            v-model="answer"
            :class="answerClass"
            :size="stringLength"
            :maxlength="stringLength"
            :disabled="isStateDone || isStateReady"
            tabindex="-1">
    </p>
    <p>
        Correct answer: {{ correctAnswer || '____' }}
    </p>
    <p>
        <button @click="startGame()" :disabled="isStateActive">
            Start
        </button>
        <button @click="stopGame()" :disabled="!isStateActive">
            Stop
        </button>
    </p>
    <p>
        {{ summary || "." }}
    </p>
</div>
</template>

<style>
#core {
    width: 150px;
    text-align: right;
    margin: 50px auto;
}
.answer-good {
    background-color: #57dd41;
}
.answer-bad {
    background-color: #F08080;
}
</style>

<script>

const State = {
    ready: 0,
    round: 1,
    check: 2,
    done: 3
}

const initialSummary = 'press enter to start'

export default {
    name: 'core',
    props: {
        roundTimeInSeconds: {
            type: Number,
            default: 2,
            validator: (v) => v >= 1 && v <= 100
        },
        checkTimeInSeconds: {
            type: Number,
            default: 1,
            validator: (v) => v >= 1 && v <= 100
        },
        timerScale: {
            type: Number,
            default: 1,
            validator: (v) => v >= 1 && v <= 1000
        },
        stringLength: {
            type: Number,
            default: 4,
            validator: (v) => v >= 1 && v <= 10
        },
        shiftValue: {
            type: Number,
            default: 1,
            validator: (v) => v >= 1 && v <= 9
        }
    },
    data () {
        return {
            state: State.ready,
            timeLeft: 0,
            intervalID: 0,
            question: '',
            answer: '',
            correctAnswer: '',
            correctCount: 0,
            totalCount: 0,
            summary: initialSummary
        }
    },
    computed: {
        isAnswerCorrect () { return this.answer === this.correctAnswer },
        isStateReady () { return this.state === State.ready },
        isStateRound () { return this.state === State.round },
        isStateCheck () { return this.state === State.check },
        isStateDone () { return this.state === State.done },
        isStateActive () { return this.isStateRound || this.isStateCheck },
        answerClass () {
            let className = 'answer-'
            if (this.isStateCheck || this.isStateDone) {
                className += this.isAnswerCorrect ? 'good' : 'bad'
            } else {
                className += 'none'
            }
            return className
        },
        roundTime () { return this.roundTimeInSeconds * 1000 },
        checkTime () { return this.checkTimeInSeconds * 1000 },
        timeStep () {
            return (this.isStateRound ? this.roundTime : this.checkTime) /
                1000 * this.timerScale
        },
        stateText () { return Object.keys(State)[this.state] }
    },
    mounted () {
        window.addEventListener('keyup', this.handleKeyUp)
    },
    methods: {
        startGame () {
            this.correctCount = this.totalCount = 0
            this.summary = ''
            this.startRound()
        },
        startRound () {
            this.timeLeft = this.intervalID = 0
            this.question = this.answer = this.correctAnswer = ''
            setTimeout(() => { this.$refs.answerInput.focus() }, 0)
            this.setQuestion()
            this.state = State.round
            this.setInterval()
        },
        endRound () {
            this.checkAnswer()
            this.state = State.check
            this.setInterval()
        },
        stopGame () {
            this.state = State.done
            this.clearInterval()
            this.checkAnswer()
            this.setSummary()
        },
        setQuestion () {
            let string = ''
            for (let i = 0; i < this.stringLength; ++i) {
                string += Math.floor(Math.random() * 9)
            }
            this.question = string
            ++this.totalCount
        },
        checkAnswer () {
            let correctAnswer = ''
            for (let i = 0; i < this.question.length; ++i) {
                correctAnswer += (this.shiftValue + +this.question[i]) % 10
            }
            this.correctAnswer = correctAnswer
            if (this.isAnswerCorrect) {
                ++this.correctCount
            }
        },
        setInterval () {
            this.timeLeft = this.isStateRound ? this.roundTime : this.checkTime
            this.intervalID = setInterval(this.tick, this.timeStep)
        },
        clearInterval () {
            clearInterval(this.intervalID)
        },
        tick () {
            this.timeLeft -= this.timeStep
            if (this.timeLeft <= 0) {
                this.clearInterval()

                if (this.isStateRound) {
                    this.endRound()
                } else if (this.isStateCheck) {
                    this.startRound()
                }
            }
        },
        setSummary () {
            this.summary = '' + this.correctCount + ' / ' + this.totalCount +
                ' correct answers'
        },
        handleKeyUp (e) {
            let key = e.which || e.keyCode
            // enter key
            if (key === 13) {
                if (this.isStateActive) {
                    this.stopGame()
                } else {
                    this.startGame()
                }
            }
        }
    }
}
</script>
