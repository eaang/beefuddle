<template>
  <div>
    <section class="hero is-primary">
      <div class="hero-body">
        <p class="title">Beefuddled?</p>
        <p class="subtitle">Start searching for the right words!</p>
      </div>
    </section>
    <section class="section">
      <div class="container">
        <div class="columns">
          <!-- Input Fields -->
          <div class="column">
            <div class="title">Search Options</div>
            <b-field label="Starting Letters">
              <b-input v-model="startLetters"></b-input>
            </b-field>
            <b-field label="Required Letters">
              <b-input v-model="requiredLetters"></b-input>
            </b-field>
            <b-field label="Excluded Letters">
              <b-input v-model="excludedLetters"></b-input>
            </b-field>
            <b-field label="Word Length">
              <b-select v-model="wordLength" placeholder="Any" expanded>
                <option
                  v-for="option in wordOption"
                  :key="option.id"
                  :value="option"
                >
                  {{ option }} Letters
                </option>
              </b-select>
            </b-field>
            <div class="buttons">
              <b-button type="is-warning" expanded @click="getWords"
                >Generate Words</b-button
              >
              <b-button type="" expanded @click="removeNames"
                >Remove Proper Names</b-button
              >
            </div>
          </div>

          <!-- Output Section -->
          <div class="column is-three-quarters">
            <div v-if="words.length > 0">
              <div class="title">Words Found</div>
              <div v-for="i in 11" :key="i" class="block">
                <div v-if="letterNumbers(i + 3).length !== 0">
                  <div class="subtitle">{{ i + 3 }} Letters</div>
                  <div class="buttons">
                    <div
                      v-for="(word, x) in letterNumbers(i + 3)"
                      :key="x"
                      class="button is-warning is-light"
                    >
                      {{ word.word }}
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else>
              <figure class="image">
                <img src="~/assets/images/bee.png" alt="" />
              </figure>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      baseUrl: 'http://api.datamuse.com/words?sp=',
      startLetters: '',
      requiredLetters: '',
      excludedLetters: '',
      wordLength: 'Any',
      wordOption: ['Any', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14],
      words: [],
    }
  },
  computed: {
    remainingLetters() {
      if (this.wordLength === 'Any') {
        return 14 - this.startLetters.length
      } else {
        return this.wordLength - this.startLetters.length
      }
    },
    startMaxLength() {
      if (this.wordLength === 'Any') {
        return 13
      } else {
        return this.wordLength - 1
      }
    },
    fullUrl() {
      return (
        this.baseUrl +
        this.startLetters +
        '?'.repeat(this.remainingLetters) +
        '&md=p,d'
      )
    },
    urlParams() {
      const params = []
      if (this.wordLength === 'Any') {
        for (let i = this.remainingLetters; i >= 1; i--) {
          if (i + this.startLetters.length >= 4) {
            params.push(this.startLetters + '?'.repeat(i) + '&md=p')
          }
        }
      } else {
        params.push(
          this.startLetters +
            '?'.repeat(parseInt(this.wordLength) - this.startLetters.length) +
            '&md=p'
        )
      }
      return params.reverse()
    },
  },
  methods: {
    editLetters() {
      if (this.startLetters.length >= this.wordLength) {
        this.startLetters = this.startLetters.substring(0, this.wordLength - 1)
      }
    },
    editRequiredLetters() {
      const maxLength = 13 - this.startLetters.length
      if (this.requiredLetters.length >= maxLength) {
        this.requiredLetters = this.requiredLetters.substring(0, maxLength)
      }
    },
    removeNames() {
      this.words = this.words.filter(
        (entry) =>
          entry.tags !== undefined && !entry.tags.find((val) => val === 'prop')
      )
    },
    letterNumbers(x) {
      return this.words.filter((word) => word.word.length === x)
    },
    stringContainsString(word, check) {
      const arr = [word, check]
      return arr[1]
        .toLowerCase()
        .split('')
        .every((letter) => {
          return arr[0].toLowerCase().includes(letter)
        })
    },
    stringLacksString(word, check) {
      const checkArr = check.split('')
      const wordArr = []
      checkArr.forEach((letter) => {
        wordArr.push(word.includes(letter))
      })
      return !wordArr.includes(true)
    },

    // For Datamuse API
    async getWords() {
      this.words = []
      let words = []
      for (const i in this.urlParams) {
        const query = await this.$axios.get(this.baseUrl + this.urlParams[i])
        words.push(query.data)
      }
      words = [].concat
        .apply([], words)
        .filter(
          (entry) =>
            entry.word.match(/^[A-Z]+$/i) &&
            entry.score >= 90 &&
            this.stringContainsString(entry.word, this.requiredLetters) &&
            this.stringLacksString(entry.word, this.excludedLetters)
        )

      this.words = words
    },
  },
}
</script>
