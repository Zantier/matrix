<script lang="ts">
  import Matrix from "./matrix.svelte";
  import Radio from "./Radio.svelte";

  let rows1: number = 1 + Math.floor(3*Math.random());
  let rows2: number = 1 + Math.floor(3*Math.random());
  let cols1: number = 1 + Math.floor(3*Math.random());
  let cols2: number = 1 + Math.floor(3*Math.random());
  let min_val: number = -1;
  let max_val: number = 2;
  let equation = '';

  let matrix1: number[][] = []
  let matrix2: number[][] = []
  let matrix3: number[][] = []
  let is_valid: boolean;
  let selected_i1: number | undefined = undefined;
  let selected_j1: number | undefined = undefined;
  let selected_i2: number | undefined = undefined;
  let selected_j2: number | undefined = undefined;
  let modes = ['Multiply', 'Multiply (grid)', 'Convolution']
  let mode;
  $: is_convolution = mode === 'Convolution';


  function getRandom() {
    return min_val + Math.floor((max_val - min_val + 1)*Math.random());
  }

  function createMatrices() {
    matrix1 = [];
    matrix2 = [];
    for (let i = 0; i < rows1; i++) {
      let row = [];
      for (let j = 0; j < cols1; j++) {
        row.push(getRandom());
      }
      matrix1.push(row);
    }
    for (let i = 0; i < rows2; i++) {
      let row = [];
      for (let j = 0; j < cols2; j++) {
        row.push(getRandom());
      }
      matrix2.push(row);
    }
  }

  $: {
    min_val;
    max_val;
    rows1;
    cols1;
    rows2;
    cols2;
    createMatrices();
  }

  // Update matrix3
  $: {
    if (is_convolution) {
      is_valid = true;
      matrix3 = [];

      for (let i = -rows2 + 1; i < rows1; i++) {
        let row = [];
        for (let j = -cols2 + 1; j < cols1; j++) {
          let sum = 0;
          for (let i2 = 0; i2 < rows2; i2++) {
            for (let j2 = 0; j2 < cols2; j2++) {
              if (i + i2 >= 0 && i + i2 < rows1 && j + j2 >= 0 && j + j2 < cols1) {
                sum += matrix1[i + i2][j + j2] * matrix2[i2][j2];
              }
            }
          }
          row.push(sum);
        }
        matrix3.push(row);
      }
    } else {
      is_valid = cols1 === rows2;
      if (is_valid) {
        matrix3 = [];

        for (let i = 0; i < rows1; i++) {
          let row = [];
          for (let j = 0; j < cols2; j++) {
            let sum = 0;
            for (let k = 0; k < cols1; k++) {
              sum += matrix1[i][k] * matrix2[k][j];
            }
            row.push(sum);
          }
          matrix3.push(row);
        }
      }
    }
  }

  $: {
    equation = '...';
    if (selected_i1 !== undefined && selected_j2 !== undefined) {
      equation = '';
      if (is_convolution) {
        let delim = '';
        let i = selected_i1 - rows2 + 1;
        let j = selected_j2 - cols2 + 1;
        let sum = 0;
        for (let i2 = 0; i2 < rows2; i2++) {
          for (let j2 = 0; j2 < cols2; j2++) {
            if (i + i2 >= 0 && i + i2 < rows1 && j + j2 >= 0 && j + j2 < cols1) {
              let value1 = matrix1[i + i2][j + j2];
              let value2 = matrix2[i2][j2];
              let value1_str = value1.toString();
              let value2_str = value2.toString();
              if (value1 < 0) {
                value1_str = `(${value1})`;
              }
              if (value2 < 0) {
                value2_str = `(${value2})`;
              }
              let dot = '·';
              if (value1 < 0 || value2 < 0) {
                dot = '';
              }
              equation += `${delim}${value1_str}${dot}${value2_str}`;
              delim = ' + ';
            }
          }
        }
      } else {
        let delim = '';
        for (let i = 0; i < cols1; i++) {
          let value1 = matrix1[selected_i1][i];
          let value2 = matrix2[i][selected_j2];
          let value1_str = value1.toString();
          let value2_str = value2.toString();
          if (value1 < 0) {
            value1_str = `(${value1})`;
          }
          if (value2 < 0) {
            value2_str = `(${value2})`;
          }
          let dot = '·';
          if (value1 < 0 || value2 < 0) {
            dot = '';
          }
          equation += `${delim}${value1_str}${dot}${value2_str}`;
          delim = ' + ';
        }
      }

      equation += ` = ${matrix3[selected_i1][selected_j2]}`;
    }
  }


  function handle_change1(e: CustomEvent<any>): void {
    selected_i1 = e.detail.i;
    selected_j1 = e.detail.j;
  }
  function handle_change2(e: CustomEvent<any>): void {
    selected_i2 = e.detail.i;
    selected_j2 = e.detail.j;
  }
  function handle_change3(e: CustomEvent<any>): void {
    selected_i1 = e.detail.i;
    selected_j2 = e.detail.j;
  }
</script>

<h1>
  Matrix multiplication
</h1>
<div>
  Mode:
  <Radio options={modes} bind:selected={mode}></Radio>
</div>
<p>
  Matrix 1 size: <input type="number" bind:value={rows1}> x <input type="number" bind:value={cols1}>
</p>
<p>
  Matrix 2 size: <input type="number" bind:value={rows2}> x <input type="number" bind:value={cols2}>
</p>
<p>
  Values between <input type="number" bind:value={min_val}> and <input type="number" bind:value={max_val}>
</p>

<matrices class:matrix-grid={mode === 'Multiply (grid)'} class:is_valid>
  <div class="matrix1"><Matrix bind:matrix={matrix1} on:change={handle_change1}
    highlight_i={is_convolution && selected_i1 !== undefined && selected_j2 !== undefined ?
      selected_i1 - rows2 + 1 : undefined}
    highlight_j={is_convolution && selected_i1 !== undefined && selected_j2 !== undefined ?
      selected_j2 - cols2 + 1 : undefined}
    highlight_height={rows2}
    highlight_width={cols2}
    highlight_row={is_convolution ? undefined : selected_i1} highlight_col = {undefined}
    --highlight="#204020"
  /></div>
  <dot>·</dot>
  <div class="matrix2"><Matrix bind:matrix={matrix2} on:change={handle_change2}
    highlight_i={is_convolution && selected_i1 !== undefined && selected_j2 !== undefined ?
      -selected_i1 + rows2 - 1 : undefined}
    highlight_j={is_convolution && selected_i1 !== undefined && selected_j2 !== undefined ?
      -selected_j2 + cols2 - 1 : undefined}
    highlight_height={rows1}
    highlight_width={cols1}
    highlight_row={undefined} highlight_col = {is_convolution ? undefined : selected_j2}
    --highlight="#204020"
  /></div>
  <equals>=</equals>
  {#if is_valid}
    <div class="matrix3"><Matrix matrix={matrix3} on:change={handle_change3}
      editable={false}
      highlight_i={selected_i1} highlight_j={selected_j2}
      highlight_height={1} highlight_width={1}
      highlight_row={undefined}
      highlight_col={undefined}
      --highlight="#404020"
    /></div>
  {:else}
    <div class="matrix3">invalid</div>
  {/if}
</matrices>
<equation>
  {equation}
</equation>
<p>
  [Hover over] / [tap] values to see highlighting
</p>

<style>
  :root {
    background-color: #202020;
    color: #a0a0a0;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
      Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    font-size: 20px;
  }
  input {
    width: 50px;
    background-color: inherit;
    color: inherit;
    border-color: #202020;
    font-size: 30px;
    margin: 0 10px;
  }
  input:focus {
    outline-color: #a0a0a0;
    outline-style: solid;
  }
  matrices {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 10px;
    font-size: 20px;
  }
  .matrix-grid {
    display: grid;
    grid-template-columns: max-content max-content;
  }
  .matrix-grid > .matrix1 {
    grid-row-start: 2;
    grid-column-start: 1;
  }
  .matrix-grid > .matrix2 {
    grid-row-start: 1;
    grid-column-start: 2;
  }
  .matrix-grid > .matrix3 {
    grid-row-start: 2;
    grid-column-start: 2;
  }
  .matrix-grid > equals {
    display: none;
  }
  :not(.is_valid) > .matrix3 {
    background-color: #402020;
    text-align: center;
  }
  dot {
    text-align: center;
    font-size: 2em;
    font-family: initial;
  }
  equation {
    display: block;
    padding: 20px 10px;
  }
</style>
