<script>
	import { base } from '$app/paths';
    import { page } from '$app/stores';
    import dayjs from 'dayjs';
	import { InlineCalendar, themes } from 'svelte-calendar';
	// const { dark: theme } = themes;
    let store;
    const today = new Date();
    let id=$page.url.searchParams.get('id')
	const tomorrow = dayjs().add(1, 'day').toDate();
    const theme = {
		calendar: {
			width: '600px',
			shadow: '0px 0px 5px rgba(0, 0, 0, 0.25)',
		},
        
	};
    function sendDate(){
        // console.log("store",$store.selected)
            let dateSel=dayjs($store.selected).format('YYYY-MM-DD');
            console.log(dateSel)
            window.location.href = `${ base }/item?id=${id}&date=${dateSel}`;
    }
</script>
<div class="centered-div">
    <h1>Select A Date</h1>
  </div>
<div class="vertical-center">
    
    <InlineCalendar {theme}  start={today} end={tomorrow} bind:store />
    <div class="button-container">
    <button class="custom-button" on:click={sendDate} >Next </button>
  </div>
</div>

<style>
    .centered-div {
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 150px; /* Add padding around the div */
    }

    /* Add some styles to the h1 element */
    h1 {
      text-align: center;
      padding: 10px; /* Padding inside the h1 element */
    }
    .vertical-center {
        margin: 0;
        position: absolute;
        top: 50%;
        left: 50%;
        -ms-transform: translate(-50%, -50%);
        transform: translate(-50%, -50%);
    }
    .button-container {
    position: absolute;
    padding-top: 70px;
  }

  /* Style for the button */
  .custom-button {
    background-color: #00bcf5; /* Blue color for the button */
    color: white;
    border: none;
    border-radius: 20px;
    width:600px;
    padding: 10px 20px;
    cursor: pointer;
  }
  .mi {
		font-size: 1.4rem;
	}
</style>