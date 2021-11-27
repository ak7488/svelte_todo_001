<script>
  import moment from "moment";

  import { createEventDispatcher } from "svelte";

  let task = "";
  let importance = 1;
  let description = "";
  let time = moment(new Date()).format("YYYY-MM-DDThh:mm");

  const dispatch = createEventDispatcher();

  const reset = () => {
    task = "";
    importance = 1;
    description = "";
    time = moment(new Date()).format("YYYY-MM-DDThh:mm");
  };

  const addItemHandler = async () => {
    if (!task || !description || !importance || !time) return;
    const composedData = {
      id: `${new Date().getTime()}-${Math.random()}`.replace(/\./g, ""),
      task,
      description,
      expires: new Date(time).getTime(),
      importance,
    };
    const alreadySavedDataJSON = await localStorage.getItem("item_data");
    if (!alreadySavedDataJSON) {
      await localStorage.setItem("item_data", JSON.stringify([]));
    }
    const alreadySavedData = JSON.parse(alreadySavedDataJSON);
    await window.localStorage.setItem(
      "item_data",
      JSON.stringify([composedData, ...alreadySavedData])
    );
    dispatch("addItem_data", composedData);
    reset();
  };
</script>

<div class="add-item">
  <input type="text" placeholder="Task" bind:value={task} maxlength={30} />
  <input
    type="text"
    placeholder="Description"
    bind:value={description}
    maxlength="200"
  />
  <input
    type="datetime-local"
    max={moment(new Date().getTime() + 3600 * 1000 * 24 * 30).format(
      "YYYY-MM-DDThh:mm"
    )}
    min={moment(new Date()).format("YYYY-MM-DDThh:mm")}
    bind:value={time}
  />
  <select bind:value={importance}>
    {#each Array(10) as _, i}
      <option value={i + 1}>Importance level: {i + 1}</option>
    {/each}
  </select>
  <div class="add_item_button_container">
    <button on:click={addItemHandler}>Add Task</button>
    <button on:click={reset}>Reset</button>
  </div>
</div>

<style>
  .add-item {
    width: 95%;
    background: linear-gradient(45deg, #ba81ff, #ff6a95);
    padding: 20px;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .add_item_button_container,
  .add-item > input,
  .add-item > select {
    width: 100%;
    padding: 7px;
    border-radius: 5px;
    border: 2px solid violet;
    margin-top: 10px;
    max-width: 600px;
    outline: none;
  }

  .add_item_button_container {
    border: none;
    display: flex;
    padding: 10px 0px;
    justify-content: space-between;
  }

  .add_item_button_container > button {
    background: whitesmoke;
    color: rgb(199, 28, 199);
    padding: 7px;
    width: 100px;
    border: none;
    border-radius: 5px;
    margin-right: 10px;
    transition: box-shadow 0.3s ease-in-out, transform 0.3s ease-in-out;
  }

  .add_item_button_container > button:hover {
    box-shadow: 3px 3px 5px black;
    transform: scale(1.02);
  }
</style>
