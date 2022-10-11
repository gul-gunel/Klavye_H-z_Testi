<template>
	<div class="jumbotron container p-4 mt-2 bg-light card">
		<h1 class="display-4">Klavye Hız Testi</h1>
		<p class="lead">Ne kadar hızlı klavye kullandığını test et...</p>
		<p> Doğru Sayısı : {{trueCount}} Yanlış Sayısı : {{falseCount}} </p>
		<hr class="my-4">
		<div v-if="isFinish" class="alert alert-primary">
			<h3>Süre Bitti</h3>
			<p class="display-4">{{ dks }} DKS</p>
			<span>Doğruluk Yüzdesi : %{{ dy }}</span> <br>
			<span>Doğru Kelime Sayısı : {{trueCount}}</span> <br>
			<span>Yanlış Kelime Sayısı : {{falseCount}}</span> <br>
			<button @click="newGame" class="btn btn-dark btn-block mt-3">Yeni Oyun</button>
		</div>
		<div v-else>
			<div class="card mb-2">
				<div class="card-body">
					<span v-for="(word,i) in words.filter((data, index) => index < 25)" :key="i"
						:class="i !== 0 || nextWordControl" class="me-1 py-1 px-2 rounded words">
						{{word}}
					</span>
				</div>
			</div>
			<div class="input-group">
				<input type="text" class="form-control" v-model="writingWord">
				<div class="input-group-append" id="button-addon4">
					<button class="btn btn-secondary disabled" type="button">{{ timer }} sn.</button>
					<button :disabled="isRunning" @click="getWords" class="btn btn-secondary"
						type="button">Yenile</button>
				</div>
			</div>
		</div>
	</div>
</template>
<script>
import wordList from '../assets/words.json'
export default {
	data() {
		return {
			words: [],
			writingWord: null,
			isTrue: true,
			trueCount: 0,
			falseCount: 0,
			timer: 60, // 3dk
			interval: false,
			isRunning: false,
			isFinish: false,
			wordList: wordList
		}
	},
	watch: {
		writingWord(val) {
			if (!val || val === ' ') {
				this.writingWord = ''
				return
			}
			if (!this.isRunning) this.toggleTimer();
			const word = this.words[0].slice(0, val.length).toUpperCase();  // parça al
			const userWord = val.replace(' ', '').toUpperCase();
			this.isTrue = word === userWord

			if (val.indexOf(' ') !== -1) {  // boşluk tuşuna basılma kontrolü
				this.isTrue ? this.trueCount++ : this.falseCount++
				this.words.splice(0, 1);  // arrayın 0. elemanından başla 1 sil
				this.writingWord = '';  // space basılınca input içerisi temizlenecek
				this.isTrue = true;
			}
		}
	},
	computed: {
		nextWordControl() {
			return this.isTrue ? 'next-word' : 'next-word bg-danger'
		},
		dks() {
			return 300 - this.words.length
		},
		dy() {
			const percent = ((this.trueCount / (this.trueCount + this.falseCount)) * 100).toFixed(2);
			return isNaN(percent) ? 0 : percent
		}
	},
	mounted() {
		this.getWords()
	},
	methods: {
		newGame() {
			this.getWords();
			this.isFinish = false;
			this.timer = 60;
			this.isTrue = true;
			this.isRunning = false
		},
		getWords() {
			this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
		},
		toggleTimer() {
			this.isRunning = true;
			this.interval = setInterval(this.timeProcess, 1000);  // her bir sn'de bir timeProcess fonksiyonu çalışacak
		},
		timeProcess() {
			if (this.timer === 0) {
				clearInterval(this.interval);
				this.isFinish = true
				return
			}
			this.timer--
		}
	}
}
</script>
<style>
.words {
	font-size: 1.2rem;
}

.next-word {
	background-color: rgb(187, 191, 228);
}
</style>
