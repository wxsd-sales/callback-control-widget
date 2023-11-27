<script>
  import { base } from '$app/paths';
  import { page } from '$app/stores';
  // import { items } from '../data.json';
  import { onMount } from 'svelte';
  import axios from 'axios';

  let item;
  let values=[];
  let confDialog;
  let thresholdDialog;
  let id=$page.url.searchParams.get('id')
  let editingRow = null;
  let todayDay;
  let itemName=null;

  function startEditing(row) {
    editingRow = row;
  }

  function stopEditing() {
    editingRow = null;
  }

  onMount( async () => {
		confDialog = document.getElementById('confirmation-dialog');
    thresholdDialog = document.getElementById('threshold-dialog');
    let daysOfWeek = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
    let today = new Date();
    todayDay = daysOfWeek[today.getDay()];
      await axios.get('https://bosch-server.wbx.ninja/items').then((r)=>{
      console.log(r)
      item = r.data[id-1];
        values = item.details;
        itemName=item.name;
        console.log(itemName)
    }).catch((e)=>{
      console.log("error",e)
    })
  })


  async function submitValue(){
    console.log("in submit",values)
    showConfDialogClick()
    const data={
      "data":values
    }
    await axios.put(`https://bosch-server.wbx.ninja/items/${id}`,data).then((r)=>{
      console.log(r)
    }).catch((e)=>{
      console.log("error",e)
    })
  }

  async function onSubmit(e) {
    const formData = new FormData(e.target);
		
		const data = {};
		for (let field of formData) {
			const [key, value] = field;
			data[key] = value;
		}
		console.log(data)
    const thresdata={
      "threshold":data.threshold
    }
    await axios.put(`https://bosch-server.wbx.ninja/threshold/${itemName}`,data).then((r)=>{
      console.log(r)
      closeThresClick()
    }).catch((e)=>{
      console.log("error",e)
    })
  }
  function backButton(){
    window.location.href = `${ base }/`;
  }

  const showConfDialogClick = () => {
			confDialog['showModal']();
	};
	const closeConfClick = () => {
    window.location.reload();
		confDialog.close();
	};
  const showThresDialogClick = () => {
    thresholdDialog['showModal']();
	};
	const closeThresClick = () => {
		thresholdDialog.close();
	};

</script>

<main>
  <h1 class="item-heading">Options</h1> 
  <table class="custom-table">
    <thead>
      <tr>
        <th>Name</th>
        <th>Section</th>
        <th>Key</th>
        <th>Value</th>
      </tr>
    </thead>
    <tbody>
      {#if values}
      {#each values as val, i (i)}
      {#if val.key.split('-')[2]!== todayDay}
        <tr
          class:table-row-even={i % 2 === 0}
          class:table-row-odd={i % 2 !== 0}
        >
          <td>{val.name}</td>       
          <td>{val.section}</td>
          <td>{val.key}</td>
          <td>{#if editingRow === val}
            <input type="text" bind:value={val.value} on:blur={stopEditing} />
            {:else}
            <span on:click={() => startEditing(val)}>{val.value}</span>
            {/if}
          </td>
          
        </tr>
        {/if}
      {/each}
      {/if}
    </tbody>
  </table>
  <div class="button-container">
    <button class="custom-button" on:click={submitValue}><span>Submit</button>
  </div>
  <div class="back-button-container">
    <button class="back-custom-button" on:click={backButton}>Go Back</button>
  </div>

  <div class="threshold-button-container">
    <button class="back-custom-button" on:click={showThresDialogClick}>Change Threshold</button>
  </div>
  <dialog id="confirmation-dialog">
		<div class="content">
			<h3>
				Your changes have been saved!
			</h3>
			<button class="button is-danger is-outlined is-rounded" on:click={closeConfClick}>Close</button>
		</div>
	</dialog>
  <dialog id="threshold-dialog">
      <form class="content" on:submit|preventDefault={onSubmit}>
        <div class="section">
          <label class="label" for="nameLabel">Threshold</label>
          <div class="control">
            <input class="input" type="text" id="threshold" name="threshold" placeholder="Enter threshold" required>
          </div>
        </div>
        
        <hr class="solid">
        <div class="columns is-multiline is-mobile">
          <div class="column">
            <button type="submit" class="button is-success is-rounded is-fullwidth" >Submit</button>
          </div>
          <div class="column">
            <button type="reset" class="button is-danger is-outlined is-rounded is-fullwidth" on:click={closeThresClick}>Cancel</button>
          </div>
        </div>
      </form>
	</dialog>
</main>

<svelte:head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.4/css/bulma.min.css">
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma-calendar@6.1.14/dist/css/bulma-calendar.min.css">
</svelte:head>

<style>
  /* Define styles for the alternating row colors */
  .table-row-odd {
    background-color: white;
  }
  .table-row-even {
    background-color: #f0f0f0; /* Light gray */
  }

  .custom-table {
    width: 80%; /* Set the table width to 80% */
    margin: 0 auto; /* Center the table horizontally */
    border-collapse: collapse; /* Collapse borders to style table cells */
    position: relative; /* Make the table a positioned container */
  }

  .custom-table th {
    text-align: left; /* Align the heading text to the left */
    background-color: #ddd; /* Gray background for the heading */
    padding: 10px; /* Add padding to the heading cells */
  }

  .custom-table th.id-column {
    width: 20px; /* Set the width of the ID column */
  }

  .custom-table th, .custom-table td {
    padding: 10px; /* Add padding to the cells */
  }

  .custom-table td {
    text-align: left; /* Align the content in cells to the left */
  }

  .custom-table tr {
    height: 50px; /* Set the row height to 50px */
    cursor: pointer; /* Change the cursor to a pointer on hover */
  }

  .item-heading {
    padding: 10px; /* Add padding to the heading */
    margin: 40px 10%; /* Reset margin to 0 */
    /* margin-bottom: 40px; */
    font-size: 25px;
    font-weight: bold;
    /* margin-right:50%; */
  }

  /* Style for the button container */
  .button-container {
    position: relative;
    bottom: 10%;
    left: 80%;
    right:20%;
    padding-top: 40px;
  }

  /* Style for the button */
  .custom-button {
    background-color: #00bcf5; /* Blue color for the button */
    color: white;
    border: none;
    border-radius: 20px;
    width:150px;
    padding: 10px 20px;
    cursor: pointer;
  }
  dialog {
		border-radius: 10px;
		width: 50%;
		transition: all 2s;
		background-color: white;
	}
	dialog::backdrop {
		background: linear-gradient(rgba(0,0,0,0.4), rgba(0,0,0,0.7));
		animation: fade-in 1s;
	}
  .disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
.back-button-container {
    position: absolute;
    top: 40px;
    right: 10%;
    padding: 10px;
  }

  .threshold-button-container {
    position: absolute;
    top: 40px;
    right: 18%;
    padding: 10px;
  }

  /* Style for the button */
  .back-custom-button {
    background-color: #f0f0f0; /* Blue color for the button */
    color: black;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
  }
</style>