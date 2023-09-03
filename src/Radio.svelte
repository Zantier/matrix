<script lang="ts">
  export let options;
  export let selected = options[0];
  const uniqueID = Math.floor(Math.random() * 1000).toString();
</script>

<div class="outer">
  {#each options as option}
    <input type="radio" id={uniqueID+option} value={option} bind:group={selected}>
    <label for={uniqueID+option}> {option} </label>
  {/each}
</div>

<style>
  :root {
    --color: #101040;
  }
  .outer {
    display: inline-flex;
  }
  input {
    display: none;
  }
  label {
    padding: 10px;
    border: solid 3px var(--color);
    cursor: pointer;
  }
  input:checked + label {
    background-color: var(--color)
  }
</style>
