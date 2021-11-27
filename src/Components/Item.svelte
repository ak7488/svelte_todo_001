<script>
  import moment from "moment";
  import { createEventDispatcher } from "svelte";
  export let item = {};

  const timeLeft = (item.expires - new Date().getTime()) / 1000;
  const hoursLeft = Math.floor(timeLeft / 3600);
  const minutesLeft = Math.floor((timeLeft - hoursLeft * 3600) / 60);
  const secLeft = Math.floor(timeLeft - hoursLeft * 3600 - minutesLeft * 60);
  const dispatch = createEventDispatcher();

  const removeItemHandler = async () => {
    const itemsJSON = await localStorage.getItem("item_data");
    let items = JSON.parse(itemsJSON);
    items = items.filter((e) => e.id !== item.id);
    await localStorage.setItem("item_data", JSON.stringify(items));
    dispatch("item_change", items);
  };

  const deleteItemHandler = async () => {
    const points = await localStorage.getItem("points");
    await localStorage.setItem("points", (parseInt(points) - 10).toString());
    removeItemHandler();
    dispatch("item_deduct");
  };

  const completeHandler = async () => {
    const points = await localStorage.getItem("points");
    await localStorage.setItem("points", (parseInt(points) + 10).toString());
    removeItemHandler();
    dispatch("item_add");
  };
</script>

<div class="item">
  <h3 class="item_title">{item.task}</h3>
  <p class="item_description">{item.description}</p>
  <p class="item_importance">Importance: {item.importance}</p>
  <div class="item_part_two">
    <div class="item_button_container">
      <button class="item_button_complete" on:click={completeHandler}
        >Complete</button
      >
      <button class="item_button_delete" on:click={deleteItemHandler}
        >Delete</button
      >
    </div>
    <abbr
      title={`${hoursLeft}hours ${minutesLeft}minutes and ${secLeft}seconds are left`}
      ><p class="item_itme_indicator">
        {moment(item.expires).format("hh:mm A DD-MM")}
      </p></abbr
    >
  </div>
</div>

<style>
  .item {
    padding: 10px;
    width: 95%;
    border-radius: 10px;
    background: linear-gradient(45deg, #ba81ff, #ff6a95);
    margin-top: 10px;
    color: white;
    transition: box-shadow 0.3s ease-in-out, transform 0.3s ease-in-out;
  }
  .item:hover {
    box-shadow: 3px 3px 5px black;
    transform: scale(1.02);
  }
  .item_part_two {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    margin-top: 15px;
  }
  .item_button_container > button {
    background: whitesmoke;
    color: rgb(209, 53, 209);
    padding: 7px;
    width: 100px;
    border: none;
    border-radius: 5px;
    margin-right: 10px;
    transition: box-shadow 0.3s ease-in-out, transform 0.3s ease-in-out,
      background 0.3s ease-in-out, color 0.3s ease-in-out;
  }
  .item_button_container > button:hover {
    box-shadow: 3px 3px 5px black;
    transform: scale(1.02);
  }
  .item_button_delete:hover {
    background: red;
    color: whitesmoke;
  }
  .item_itme_indicator {
    padding-top: 10px;
  }
  abbr {
    text-decoration: none;
  }
</style>
