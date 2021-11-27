<script>
  import Item from "./Components/Item.svelte";
  import AddItem from "./Components/AddItem.svelte";
  import { onMount } from "svelte";
  let data = [];
  let points = 0;

  const addItemDataHandler = (e) => {
    data = [e.detail, ...data];
  };

  const changeItemHandler = (e) => {
    data = e.detail;
  };

  onMount(async () => {
    const dataJSON = await window.localStorage.getItem("item_data");
    const pointsJSON = await window.localStorage.getItem("points");
    if (!dataJSON) {
      await window.localStorage.setItem("item_data", JSON.stringify([]));
    } else {
      data = JSON.parse(dataJSON);
    }
    if (!pointsJSON) {
      await window.localStorage.setItem("points", "0");
    } else {
      points = parseInt(pointsJSON);
    }
  });
</script>

<main>
  <AddItem on:addItem_data={addItemDataHandler} />
  <p class="points">Your Total Points: <span>{points}</span></p>
  <div class="item_container">
    {#each data as item}
      <Item
        {item}
        on:item_add={() => {
          points = points + 10;
        }}
        on:item_deduct={() => {
          points = points - 10;
        }}
        on:item_change={changeItemHandler}
      />
    {/each}
  </div>
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  }
  main {
    background: #fcfcff;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 10px;
  }
  .item_container {
    width: 100%;
    display: grid;
    grid-template-columns: 1fr;
    justify-items: center;
  }

  .points {
    margin: 10px 0;
    padding: 10px;
    border-radius: 7px;
    background: linear-gradient(45deg, #ba81ff, #ff6a95);
    color: white;
  }

  .points > span {
    font-weight: bold;
  }

  @media (min-width: 600px) {
    .item_container {
      grid-template-columns: 1fr 1fr;
    }
  }
  @media (min-width: 1000px) {
    .item_container {
      grid-template-columns: 1fr 1fr 1fr;
    }
  }
</style>
