<script lang="ts">
  import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

  export let matrix: number[][] = [];
  let selected_i: number | undefined = undefined;
  let selected_j: number | undefined = undefined;
  // Highlight specific cell
  export let highlight_i: number | undefined;
  export let highlight_j: number | undefined;
  // Highlight entire row or column
  export let highlight_row: number | undefined;
  export let highlight_col: number | undefined;

  function changeSelected(i: number | undefined, j: number | undefined) {
		dispatch('change', {
			i: i,
      j: j,
		});
  }

  function enter(i: number, j: number) {
    selected_i = i;
    selected_j = j;
    changeSelected(selected_i, selected_j);
  }

  function leave(i: number, j: number) {
    if (i === selected_i && j === selected_j) {
      selected_i = undefined;
      selected_j = undefined;
      changeSelected(selected_i, selected_j);
    }
  }
</script>

<outer>
  <bracket class="bracket1" />
  <matrix>
    {#each matrix as row, i}
      <row>
        {#each row as num, j}
          <val
            class:highlight_row={i === highlight_row || j === highlight_col}
            class:highlight={i === highlight_i && j === highlight_j}
            on:mouseenter={() => enter(i, j)} on:mouseleave={() => leave(i, j)} role="none">
            {num}
          </val>
        {/each}
      </row>
    {/each}
  </matrix>
  <bracket class="bracket2" />
</outer>

<style>
  :root {
    --padding: 20px;
  }
  outer {
    display: flex;
    align-items: stretch;
  }
  bracket {
    position: relative;
    width: var(--padding);
    border: 2px;
    border-radius: 3000px;
    pointer-events: none;
  }
  .bracket1 {
    left: calc(0.5 * var(--padding));
    border-left: solid;
  }
  .bracket2 {
    left: calc(-0.5 * var(--padding));
    border-right: solid;
  }
  row {
    display: block;
  }
  val {
    display: inline-block;
    line-height: 0;
    width: calc(var(--padding) * 2);
    padding: var(--padding) 0;
    text-align: center;
  }
  .highlight_row {
    background-color: #206020;
  }
  .highlight {
    background-color: #606020;
  }
</style>
