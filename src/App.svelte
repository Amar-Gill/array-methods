<script>
  import { flip } from "svelte/animate";

  let values = [];

  let activeId = null;

  let selectionSortMinValueId = null;

  let durationTime = 100;

  function resetValues() {
    values = [];

    let incrementor = 0;

    while (incrementor < 100) {
      // const randInt = Math.floor(Math.random() * 11);
      values.push({ id: incrementor, value: incrementor });
      incrementor = incrementor + 1;
    }

    shuffle(values);
  }

  function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
      let j = Math.floor(Math.random() * (i + 1)); // random index from 0 to i

      // swap elements array[i] and array[j]
      // we use "destructuring assignment" syntax to achieve that
      // same can be written as:
      // let t = array[i]; array[i] = array[j]; array[j] = t
      [array[i], array[j]] = [array[j], array[i]];
    }
  }

  // initialize the randomized array on app load
  resetValues();

  // definition of the different sorting algorithms
  function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  async function bubbleSort() {
    let sorted = false;

    while (!sorted) {
      sorted = true;

      for (let i = 1; i < values.length; i++) {
        let left = values[i - 1];
        let right = values[i];

        activeId = left.id;

        if (right.value < left.value) {
          sorted = false;
          values[i] = left;
          values[i - 1] = right;
        }

        await sleep(150);
      }
    }

    // return array;
    activeId = null;
  }

  async function selectionSort() {
    let len = values.length;
    for (let i = 0; i < len; i++) {
      // instead maybe use selectionSort specific highlighting style
      let min = i;
      activeId = values[i].id;
      selectionSortMinValueId = values[i].id;
      await sleep(420);

      for (let j = i + 1; j < len; j++) {
        activeId = values[j].id;
        if (values[min].value > values[j].value) {
          min = j;
          selectionSortMinValueId = values[j].id;
          // await sleep(2000);
        }
        await sleep(40);
      }
      if (min !== i) {
        let tmp = values[i];
        values[i] = values[min];
        values[min] = tmp;
        await sleep(600);
      }
      selectionSortMinValueId = null;
    }
    // return arr;
    activeId = null;
  }

  async function insertionSort() {
    for (let i = 1; i < values.length; i++) {
      let j = i - 1;
      let temp = values[i];
      activeId = values[i].id;
      await sleep(420);
      while (j >= 0 && values[j].value > temp.value) {
        // activeId = values[j].id;
        values[j + 1] = values[j];
        j--;
      }
      values[j + 1] = temp;
      await sleep(420);
    }
    // return values;
    activeId = null;
  }

</script>

<style>
  .basic-card {
    color: var(--hue-3);
    background-color: var(--hue-2);
    height: 2rem;
    width: 2rem;
    margin: 0.5rem;
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  }

  .flex-box {
    display: flex;
    flex-wrap: wrap;
  }

  .basic-grid {
    display: grid;
    gap: 1rem;
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  }

  .selection-sort-minimum {
    background-color: var(--comp-1);
    color: var(--comp-2);
    font-weight: bold;
  }

  [aria-current] {
    border: solid 2px var(--comp-2);
  }

  button {
    color: var(--hue-3);
    background-color: var(--hue-1);
  }

  button:disabled {
    filter: grayscale(100);
  }

  footer {
    color: var(--hue-3);
  }

  footer a {
    color: var(--comp-2)
  }

  footer a:visited {
    color: var(--comp-1)
  }

</style>

<header>
  <button disabled={activeId != null} on:click={resetValues}>reset</button>
  <button disabled={activeId != null} on:click={bubbleSort}>bubble sort</button>
  <button disabled={activeId != null} on:click={selectionSort}>
    selection sort
  </button>
  <button disabled={activeId != null} on:click={insertionSort}>
    insertion sort
  </button>
  
</header>

<main class="flex-box">
  {#each values as { id, value } (id)}
    <div
      aria-current={activeId === id ? true : undefined}
      class:selection-sort-minimum={id === selectionSortMinValueId}
      animate:flip={{ duration: 130 }}
      class="basic-card">
      {value}
    </div>
  {/each}
</main>
<footer>
  <p>Hint: refresh page to stop execution of the algorithm</p>
  <p>
    Created by
    <a href="https://amar-gill.now.sh" alt="developer website" target="_blank">A. Gill</a>
  </p>
</footer>