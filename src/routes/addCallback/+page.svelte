<!-- AddCallback.svelte -->
<script>
    import { base } from '$app/paths';
    import dayjs from 'dayjs';
    import axios from 'axios';
    let name = '';
    let selectedOptions = [];
    let callback = '';
    const today = new Date();
    const tomorrow = dayjs().add(1, 'day').toDate();

    async function handleSubmit() {
        console.log('Name:', name);
        console.log('Selected Options:', selectedOptions);
        console.log('Callback:', callback);
        const details=[]
        for(let i=0;i<selectedOptions.length;i++){
            details.push({
                "name": `${name}/callback-capacity`,
                "section": `${name}`,
                "key": `${selectedOptions[i]}`,
                "value": callback
            })
        }
        const data={
            "name":name,
            "section":"",
            "key":"",
            "value":"",
            "details":details
        }
        await axios.post(`https://bosch-server.wbx.ninja/items`,data)
        .then((r)=>{
            window.location.href = `${ base }/`;
            console.log(r)
        }).catch((e)=>{
            console.log("error",e)
        })
    }
</script>

<style>
  main {
    text-align: left;
    padding: 40px 250px;
  }

  h1 {
    font-size: 24px;
    margin-bottom: 70px;
  }

  form {
    display: flex;
    flex-direction: column;
    padding:40px 0px;
  }

  label {
    margin-top: 10px;
  }

  input,
  select {
    
    padding: 8px;
    margin: 5px 0;
    width: 80%;
  }

  /* Style the select element to look like a dropdown */
  select {
    appearance: none;
    -webkit-appearance: none;
    -moz-appearance: none;
    background: transparent;
    border: 1px solid #000;
    padding: 8px;
    border-radius: 5px;
    cursor: pointer;
    width: 82%;
    height:160px;
  }

  select::after {
    content: '▼'; /* Custom dropdown arrow symbol */
    position: absolute;
    top: 50%;
    right: 10px;
    transform: translateY(-50%);
    pointer-events: none;
  }

  button {
    margin-top: 60px;
    background-color: #007BFF;
    color: #fff;
    border: none;
    padding: 10px 40px;
    cursor: pointer;
    width: 82%;
    border-radius: 20px;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>

<main>
  <form on:submit={handleSubmit}>
    <h1>Add Callback</h1>
    <label for="name" style="align-items: left;"><b>Name:</b></label>
    <input type="text" id="name" bind:value={name} required/>

    <label for="options"><b>Select callback slots:</b></label>
    <select id="options" multiple bind:value={selectedOptions} required>
      <option value="callback-capacity-Monday-AM">callback-capacity-Monday-AM</option>
      <option value="callback-capacity-Monday-PM">callback-capacity-Monday-PM</option>
      <option value="callback-capacity-Tuesday-AM">callback-capacity-Tuesday-AM</option>
      <option value="callback-capacity-Tuesday-PM">callback-capacity-Tuesday-PM</option>
      <option value="callback-capacity-Wednesday-AM">callback-capacity-Wednesday-AM</option>
      <option value="callback-capacity-Wednesday-PM">callback-capacity-Wednesday-PM</option>
      <option value="callback-capacity-Thursday-AM">callback-capacity-Thursday-AM</option>
      <option value="callback-capacity-Thursday-PM">callback-capacity-Thursday-PM</option>
      <option value="callback-capacity-Friday-AM">callback-capacity-Friday-AM</option>
      <option value="callback-capacity-Friday-PM">callback-capacity-Friday-PM</option>
      <option value="callback-capacity-Saturday-AM">callback-capacity-Saturday-AM</option>
      <option value="callback-capacity-Saturday-PM">callback-capacity-Saturday-PM</option>
      <option value="callback-capacity-Sunday-AM">callback-capacity-Sunday-AM</option>
      <option value="callback-capacity-Sunday-PM">callback-capacity-Sunday-PM</option>
    </select>

    <label for="callback"><b>Callbacks per slot:</b></label>
    <input type="text" id="callback" bind:value={callback} required/>

    <button type="submit">Submit</button>
  </form>
</main>
