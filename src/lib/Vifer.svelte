<script>
  import { fives } from './fives'
  const FIVES = fives.map((word) => word.toUpperCase())

  let word = selectWord()
  let guesses = []
  let newGuess = ''
  let showAnswer = false

  $: valid = newGuess.length === 5

  function guess() {
    if (valid) {
      guesses = [...guesses, newGuess]
      newGuess = ''
    }
  }

  function handleKeypress(event) {
    newGuess = newGuess.toUpperCase()
    if (event.key === 'Enter') {
      guess()
    }
  }

  const SUM = (a, b) => a + b

  function matching(word, guess) {
    const guessSet = new Set(guess.split(''))
    return Array.from(guessSet)
      .map((letter) => word.includes(letter))
      .reduce(SUM, 0)
  }

  function position(word, guess) {
    return [...Array(word.length).keys()]
      .map((i) => word[i] === guess[i])
      .reduce(SUM, 0)
  }

  // Return a random word that has 5 different letters in it
  function selectWord() {
    const uniqueLetters = (word) => new Set(word.split('')).size
    let word = ''
    do {
      const i = Math.floor(Math.random() * FIVES.length)
      word = FIVES[i]
    } while (uniqueLetters(word) < 5)
    return word
  }

  function newWord() {
    word = selectWord()
    guesses = []
    showAnswer = false
  }
</script>

<div class="vifer">
  {#if guesses.length > 0}
    <table>
      <tbody>
        <tr>
          <td>Guesses</td>
          <td>Matching</td>
          <td>In Position</td>
        </tr>
        {#each guesses as g}
          <tr>
            <td>{g}</td>
            <td>{matching(word, g)}</td>
            <td>{position(word, g)}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}

  <input
    value={newGuess.toUpperCase()}
    on:input={(e) => (newGuess = e.target.value.toUpperCase())}
    on:keydown={handleKeypress}
    placeholder="Your guess"
  />
  <button on:click={guess} disabled={!valid}
    >{valid ? 'Guess!' : 'Type 5 letters'}</button
  >

  <br />
  <br />
  <br />
  {#if !showAnswer}
    <button
      on:click={() => {
        showAnswer = true
      }}>Show Answer</button
    >
  {:else}
    <button on:click={newWord}>New Word</button>
  {/if}
  <p>{showAnswer ? word : '_ _ _ _ _'}</p>
</div>

<style>
  .vifer {
    margin: 0 auto;
    text-align: center;
  }

  table,
  td {
    margin: 0 auto;
    margin-bottom: 16px;
    border: 1px solid #333;
    border-collapse: collapse;
    padding: 6px;
  }
</style>
