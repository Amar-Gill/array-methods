<script>
  export let valuesSorted;
</script>

<style>
  span {
    background-color: black;
    padding: 0.15rem;
    color: var(--hue-3);
    /* margin-left: auto; */
  }

  span span + span {
    color: var(--comp-2);
  }

  .status-sorted {
    color: greenyellow;
  }
</style>

<span>
  <span>Status:</span>
  <span class:status-sorted={valuesSorted}>
    {valuesSorted ? 'SORTED' : 'UNSORTED'}
  </span>
</span>
