<!-- src/routes/items/index.svelte -->
<script>
  // import { items } from './data.json';
  import { base } from '$app/paths';
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';
  import axios from 'axios';

  let items;
  onMount( async () => {
      await axios.get('https://bosch-server.wbx.ninja/items').then((r)=>{
      console.log(r)
      items = r.data;
    }).catch((e)=>{
      console.log("error",e)
    })
  })

  function navigateToItem(item) {
    // Pass the item's ID and name as query parameters
    goto(`item/${item.id}`, { state: { id: item.id, name: item.name } });
  }
  function handleRowClick(item) {
    // Handle the row click here (e.g., open a new tab with details)
    window.location.href = `${ base }/item?id=${item.id}`;
  }
  function addCallback() {
    // Handle the row click here (e.g., open a new tab with details)
    window.location.href = `${ base }/addCallback`;
  }
</script>

<main>
  <h1 class="item-heading">List Of Callback Properties</h1> <!-- Apply the item-heading class to the heading -->
  <table class="custom-table"> <!-- Apply the custom-table class to the table -->
    <thead>
      <tr>
        <th class="id-column"> </th> <!-- Apply the id-column class to the ID column -->
        <th>Name</th>
        <th>Threshold</th>
        <th>Key</th>
        <th>Value</th>
      </tr>
    </thead>
    <tbody>
      {#if items}
      {#each items as item (item.id)}
        <!-- Use conditional classes to style even and odd rows -->
        <tr
          class:table-row-even={item.id % 2 === 0}
          class:table-row-odd={item.id % 2 !== 0}
          on:click={() => handleRowClick(item)}
        >
          <td><i class="mi mi-caret-right mu"></i></td>
          <td>{item.name}</td>
          <td>{item.threshold}</td>
          <td>{item.key}</td>
          <td>{item.value}</td>
        </tr>
      {/each}
      {/if}
    </tbody>
  </table>
  <div class="button-container">
    <button class="custom-button" on:click={addCallback}><i class="mi mi-circle-add"></i><span class="u-sr-only">Add</span></button>
    <!-- <a class="button custom-button" href="/item">
                  <span>Add</span>
                </a> -->
  </div>
</main>
<svelte:head>
  <link rel="stylesheet" href="https://unpkg.com/mono-icons@1.0.5/iconfont/icons.css" >
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
    position: absolute;
    top: 40px;
    right: 10%;
    padding: 10px;
  }

  /* Style for the button */
  .custom-button {
    background-color: #f0f0f0; /* Blue color for the button */
    color: black;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
  }
  .mi {
    top:10px;
    font-style: normal;   
	}
  .mu {
    font-size: 1.4rem;  
	}
  .u-sr-only {
		padding-bottom:20px; 
	}
</style>