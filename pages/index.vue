<template>
  <div>
    <section class="hero is-warning">
      <div class="hero-body">
        <p class="title">Beefuddled?</p>
        <p class="subtitle">Start searching for the right words!</p>
      </div>
    </section>
    <section class="section">
      <div class="container">
        <div class="columns">
          <div class="column is-three-quarters"></div>
          <div class="column">
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
              <b-button type="is-warning is-light" expanded @click="removeNames"
                >Remove Proper Names</b-button
              >
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
      wordOption: ['Any', 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14],
      wordLength: 'Any',
      words: [],
    }
  },
}
</script>
