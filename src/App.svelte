<script>
  import { flip } from "svelte/animate";
  import { onMount } from "svelte";
  import StatusIndicator from "./StatusIndicator.svelte";

  let values = [];

  let activeId = null;

  let selectionSortMinValueId = null;

  let binarySearchLeft = null;

  let binarySearchRight = null;

  let binarySearchMiddle = null;

  let binarySearchSuccess = null;

  let useFastForward = false;

  let valuesSorted = false;

  function resetValues() {
    // for binarySearch
    binarySearchSuccess = null;
    document.getElementById("search-target").value = "";

    values = [];

    let incrementor = 0;

    while (incrementor < 120) {
      // const randInt = Math.floor(Math.random() * 11);
      values.push({ id: incrementor, value: incrementor });
      incrementor = incrementor + 1;
    }

    shuffle(values);

    valuesSorted = false;
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
  onMount(() => resetValues());

  function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  // definitions of the different sorting algorithms
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

        !useFastForward && (await sleep(150));
      }
    }

    // return array;
    activeId = null;
    useFastForward = false;
    valuesSorted = true;
  }

  async function selectionSort() {
    let len = values.length;
    for (let i = 0; i < len; i++) {
      // instead maybe use selectionSort specific highlighting style
      let min = i;
      activeId = values[i].id;
      selectionSortMinValueId = values[i].id;
      !useFastForward && (await sleep(420));

      for (let j = i + 1; j < len; j++) {
        activeId = values[j].id;
        if (values[min].value > values[j].value) {
          min = j;
          selectionSortMinValueId = values[j].id;
          // await sleep(2000);
        }
        !useFastForward && (await sleep(40));
      }
      if (min !== i) {
        let tmp = values[i];
        values[i] = values[min];
        values[min] = tmp;
        !useFastForward && (await sleep(600));
      }
      selectionSortMinValueId = null;
    }
    // return arr;
    activeId = null;
    useFastForward = false;
    valuesSorted = true;
  }

  async function insertionSort() {
    for (let i = 1; i < values.length; i++) {
      let j = i - 1;
      let temp = values[i];
      activeId = values[i].id;
      !useFastForward && (await sleep(420));
      while (j >= 0 && values[j].value > temp.value) {
        // activeId = values[j].id;
        values[j + 1] = values[j];
        j--;
      }
      values[j + 1] = temp;
      !useFastForward && (await sleep(420));
    }
    // return values;
    activeId = null;
    useFastForward = false;
    valuesSorted = true;
  }

  // TODO - review reactive variable syntax: "$"
  async function binarySearch() {
    binarySearchSuccess = null;
    const target = parseInt(document.getElementById("search-target").value);

    let left = 0;
    let right = values.length - 1;
    // apply styling and sleep
    binarySearchLeft = left;
    binarySearchRight = right;

    while (left <= right) {
      let middle = Math.floor((left + right) / 2);
      // apply styling and sleep
      binarySearchMiddle = middle;
      await sleep(1200);

      let potentialMatch = values[middle].value;

      if (target === potentialMatch) {
        await sleep(300);
        binarySearchLeft = null;
        binarySearchRight = null;
        binarySearchMiddle = null;
        binarySearchSuccess = middle;
        await sleep(1500);
        // binarySearchSuccess = null;
        console.log(values[middle].value);
        return 0;
      } else if (target < potentialMatch) {
        right = middle - 1;
        // apply styling and sleep
        binarySearchRight = right;
      } else {
        left = middle + 1;
        // apply styling and sleep
        binarySearchLeft = left;
      }
    }
    binarySearchLeft = null;
    binarySearchRight = null;
    binarySearchMiddle = null;
    return -1;
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
    font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
      "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
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

  .search-pointer-ends {
    border: solid 2px var(--comp-2);
  }

  .search-pointer-middle {
    background-color: var(--comp-1);
    color: var(--comp-2);
    font-weight: bold;
  }

  .search-pointer-success {
    background-color: green;
    color: yellow;
    font-weight: bold;
  }

  button {
    color: var(--hue-3);
    background-color: var(--hue-1);
    /* max-width: 60px; */
  }

  button:disabled {
    filter: grayscale(100);
  }

  footer {
    color: var(--hue-3);
    padding-left: 0.5rem;
  }

  footer a {
    color: var(--comp-2);
  }

  footer a:visited {
    color: var(--comp-1);
  }

  header {
    padding-left: 0.5rem;
    padding-bottom: 0.5rem;
    padding-right: 1.4rem;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
  }

  header * {
    /* margin-right: 0.5rem; */
    margin-bottom: .5rem;
  }

  span:first-child {
    margin-right: 0.6rem;
  }

  span:first-child button{
    width: 95px;
  }

  @media only screen and (max-width: 528px) {
    span:first-child {
      max-width: 240px;
    }
  }

  /* for positioning status indicator */
  @media only screen and (min-width: 1024px) {
    span:last-child {
      margin-left: auto;
    }

  }
</style>

<header>
  <span>
    <button disabled={activeId != null} on:click={bubbleSort}>
      Bubble Sort
    </button>
    <button disabled={activeId != null} on:click={selectionSort}>
      Selection Sort
    </button>
    <button disabled={activeId != null} on:click={insertionSort}>
      Insertion Sort
    </button>
    <button
      disabled={activeId == null}
      on:click={() => (useFastForward = !useFastForward)}>
      Fast Forward
    </button>
  </span>

  <span>
    <button disabled={activeId != null} on:click={resetValues}>Reset</button>
    <StatusIndicator {valuesSorted} />
  </span>

</header>
<header>
  <input
    id="search-target"
    disabled={!valuesSorted}
    placeholder="search for a number"
    type="number" />
  <button disabled={!valuesSorted} on:click={binarySearch}>
    Binary Search
  </button>
</header>

<main class="flex-box">
  {#each values as { id, value } (id)}
    <div
      aria-current={activeId === id ? true : undefined}
      class:selection-sort-minimum={id === selectionSortMinValueId}
      class:search-pointer-ends={id === binarySearchLeft || id === binarySearchRight}
      class:search-pointer-middle={id === binarySearchMiddle}
      class:search-pointer-success={id === binarySearchSuccess}
      animate:flip={{ duration: 130 }}
      class="basic-card">
      {value}
    </div>
  {/each}
</main>
<footer>
  <p>
    Created by
    <a href="https://amar-gill.now.sh" alt="developer website" target="_blank">
      A. Gill
    </a>
  </p>
  <a
    href="https://github.com/Amar-Gill/array-methods"
    alt="source code"
    target="_blank">
    Source Code
  </a>
</footer>
