<template>
	<div class="cont">
		<div class="box start" v-if="!started">
			<div><p>欢迎来到我们题答系统</p></div>
			<div>
				<button @click="start()">开始</button>
			</div>
		</div>
		<div class="box finish" v-else-if="finished">
			<div><p>感谢参观调查</p></div>
			<div><p>我们会联系您</p></div>
		</div>
		<div class="box" v-else>
			<div>
				<p>
					<strong>{{ currentQuestion + 1 }}.</strong>
					{{ questions[currentQuestion].text }}
				</p>
			</div>
			<div
				v-for="(opt, i) in questions[currentQuestion].options"
				@click="select(i)"
				class="option"
				:class="{ selected: picked[currentQuestion] == i }"
			>
				<p>
					<strong>{{ opt.k }}.</strong> {{ opt.v }}
				</p>
			</div>
			<div>
				<!-- <button
					@click="skip()"
					:class="{
						inactive: !canSkip,
					}"
				>
					跳过
				</button> -->
				<!-- <button @click="quit()">退出</button> -->

				<button :class="{ inactive: !canPrev }" @click="prev()">上一个</button>
				<button @click="next()" :class="{ inactive: !canNext }">
					{{ currentQuestion == questions.length - 1 ? '结束' : '下一个' }}
				</button>
			</div>
		</div>
	</div>
	<div v-if="showScore">
		<b-card title="Results" style="max-width: 20rem">
			You Scored {{ score }} of {{ questions.length }}
		</b-card>
	</div>
</template>

<script lang="ts">
import d from '../assets/data.json'
type options = {
	k: String
	v: String
	correct?: boolean
}
type question = {
	text: String
	options: options[]
}
let data: question[] = d

export default {
	data() {
		return {
			questions: data,
			currentQuestion: 0,
			showScore: false,
			score: 0,
			countDown: 30,
			timer: 0,
			finished: false,
			started: false,
			canSkip: true,
			canNext: false,
			canPrev: false,
			picked: new Array(data.length).fill(-1),
			questions_old: [
				{
					questionText: 'Which one is used for two-way binding?',
					answerOptions: [
						{ answerText: 'v-on', isCorrect: false },
						{ answerText: 'v-bind', isCorrect: false },
						{ answerText: 'v-model', isCorrect: true },
						{ answerText: 'v-if', isCorrect: false },
					],
				},
			],
		}
	},
	methods: {
		select(id: number) {
			this.picked[this.currentQuestion] =
				this.picked[this.currentQuestion] == id ? -1 : id
			this.updatePermissions()
		},
		next(force: boolean = false) {
			if (!force && !this.canNext) return
			if (this.currentQuestion == this.questions.length - 1) {
				//finish
				this.finished = true
			} else {
				this.currentQuestion++
				this.set(this.currentQuestion)
			}
		},
		prev() {
			if (!this.canPrev) return
			if (this.currentQuestion == 0) return
			this.currentQuestion--
			this.set(this.currentQuestion)
		},
		skip() {
			if (!this.canSkip) return
			if (this.currentQuestion != this.questions.length - 1) this.next(true)
		},
		quit() {
			this.picked = new Array(data.length).fill(-1)
			this.currentQuestion = 0
			this.started = false
		},
		updatePermissions() {
			this.canSkip =
				this.currentQuestion != this.questions.length - 1 &&
				this.picked[this.currentQuestion] == -1
			this.canPrev = this.currentQuestion != 0
			this.canNext = this.picked[this.currentQuestion] != -1
		},
		set(id: number) {
			this.currentQuestion = id
			this.updatePermissions()
		},
		start() {
			this.started = true
			this.countDownTimer()
		},
		handleAnswerClick(isCorrect: boolean) {
			clearTimeout(this.timer)
			let nextQuestion = this.currentQuestion + 1
			if (isCorrect) {
				this.score = this.score + 1
			}
			if (nextQuestion < this.questions.length) {
				this.currentQuestion = nextQuestion
				// this.$store.state.questionAttended = this.currentQuestion;
				// localStorage.setItem('qattended', this.currentQuestion)
				this.countDown = 30
				this.countDownTimer()
			} else {
				// localStorage.removeItem('qattended')
				this.showScore = true
				// localStorage.setItem('testComplete',this.showScore)
			}
		},
		countDownTimer() {
			if (this.countDown > 0) {
				this.timer = setTimeout(() => {
					this.countDown -= 1
					this.countDownTimer()
				}, 1000)
			} else {
				this.handleAnswerClick(false)
			}
		},
	},
	created() {
		//  alert(this.$store.state.questionAttended)
		//    this.showScore = localStorage.getItem('testComplete') || false
		//    this.currentQuestion = localStorage.getItem('qattended') || 0
		//    this.countDownTimer()
		//    this.fetchQuiz()
	},
}
</script>

<style scoped>
.cont {
	box-sizing: border-box;
	display: flex;
	align-items: center;
	justify-content: center;
	/* height: 100vh; */
	/* background-color: #f0f0f0; */
	/* box-shadow: 2rem 0 0 rgba(0, 0, 0, 0.1) inset,
		-2rem 0 0 rgba(0, 0, 0, 0.1) inset; */
	/* background: rgba(255, 255, 255, 0.16); */
	/* backdrop-filter: blur(5px);
	-webkit-backdrop-filter: blur(5px); */
	padding: 2rem;
	flex-direction: column;
}
::selection {
	background: transparent;
}
.box {
	display: flex;
	flex-direction: column;
	width: calc(100% - 2rem);
	max-width: 1000px;
	height: auto;
	/* height: 100px; */
	gap: 1rem 0;
	border-radius: 16px;
	/* backdrop-filter: blur(5px); */
	/* -webkit-backdrop-filter: blur(5px); */
	/* border: 1rem solid rgba(255, 217, 151, 0.373); */
	padding: 2rem;
	background-color: #ffeebf;

	/* background: radial-gradient(
		farthest-corner at 100% 0,
		rgb(255, 205, 119) 0%,
		rgba(241, 210, 139, 1) 100%
	); */
}
.box div {
	display: flex;
	/* background-color: beige; */
	flex: 1;
	min-height: 100px;
	flex-direction: column;
	align-items: flex-start;
	justify-content: center;
	padding: 0 2rem;
	border: 4px solid #ffdc91;
	border-radius: 12px;
	font-size: 1.5rem;
	cursor: pointer;
	transition: all 0.2s ease;
}
.box div.selected,
.box div.selected:active,
.box div.selected:hover {
	background-color: rgb(132, 255, 132);
	border-color: rgba(59, 142, 25, 1);
	color: rgba(59, 142, 25, 1);
}
.box div:hover,
.box div:active {
	border-color: #9a344e;
}
.box div:first-child {
	cursor: initial;
	align-items: center;
	border: none;
	font-size: 2rem;
	/* background-color: blueviolet; */
}
.box div:last-child {
	cursor: initial;
	border: none;
	background-color: transparent;
	flex-direction: row;
	align-items: center;
	justify-content: flex-end;
	gap: 0 1rem;
}
.box.finish div:last-child {
	justify-content: center;
}
button {
	background-color: #9a344e;
	color: white;
	font-weight: bold;
	cursor: pointer;
	border: none;
	padding: 1rem 2rem;
	font-size: 1.5rem;
	border-radius: 0.4rem;
}

button:active {
	transform: translateY(2px);
}
button.inactive {
	background-color: #ecc1a1;
	color: #bc9a8b;
	cursor: not-allowed;
}
button.inactive:active {
	transform: none;
}

@media (max-width: 1000px) {
	.box div:first-child {
		font-size: 1.6rem;
	}
	button {
		font-size: 1.2rem;
		padding: 0.5rem 1rem;
	}
	.box div:last-child {
		padding: 0 0;
	}
}
</style>
