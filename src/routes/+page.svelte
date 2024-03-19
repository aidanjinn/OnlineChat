<!-- YourSvelteComponent.svelte -->
<script>
    import { onMount, onDestroy } from 'svelte';
    import PocketBase from 'pocketbase';
    import Message from './messages/message.svelte';

    const pb = new PocketBase('https://system-giving.pockethost.io');
    let records = []; // Initialize records as an empty array
    let intervalId; // Variable to hold the interval ID

    // Function to fetch records
    async function fetchRecords() {
        try {
            // Fetch records and update the 'records' variable
            records = await pb.collection('messages').getFullList({
                sort: '-created',
            });
        } catch (error) {
            console.error('Error fetching records:', error);
        }
    }

    // Fetch records inside an onMount function
    onMount(async () => {
        await fetchRecords(); // Initial fetch
        // Set interval to fetch records every 5 seconds
        intervalId = setInterval(fetchRecords, 5000);
    });

    // Clear interval on component destruction
    onDestroy(() => {
        clearInterval(intervalId);
    });

    // Function to update records after a message is created
    function handleMessageCreated() {
        // Re-fetch records to update the list
        fetchRecords();
    }
</script>

<style>
    /* CSS for styling */
    h1 {
        font-family: 'Arial', sans-serif;
        font-size: 2rem;
        color: #333;
        margin-bottom: 20px;
        text-align: center;
    }

    ul {
        list-style-type: none;
        padding: 0;
        margin: 0;
    }

    li {
        margin-bottom: 20px;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        overflow: hidden;
        display: flex;
        align-items: center; /* Center content vertically */
        justify-content: center; /* Center content horizontally */
        flex-direction: column; /* Stack content vertically */
    }

    pre {
        white-space: pre-wrap;
        word-wrap: break-word;
        padding: 10px;
        margin: 0;
        font-size: 1.2rem;
    }

    img {
        width: 30%;
        height: 30%;
    }

    video {
        width: 30%;
        height: 30%;
    }
    .center-div {
        display: flex;
        align-items: center; /* Center content vertically */
        justify-content: center; /* Center content horizontally */
        flex-direction: column; /* Stack content vertically */
    }
</style>

<h1>🤡 ClownChat 🤡</h1>

<div class = "center-div">
<!-- Render Message component with event listener -->
    <Message on:messageCreated={handleMessageCreated}/>
</div>

<!-- Render the fetched records -->
<ul>
    {#each records as record}
        <li>
            <pre>{record.message}</pre>
            {#if record.file && !record.file.endsWith('.mp4')}
                <!-- Display images -->
                <!-- svelte-ignore a11y-img-redundant-alt -->
                <img src={"https://system-giving.pockethost.io/api/files/ewwz4ryyhb4hv1n/" + record.id + "/" + record.file + "?token="} alt="image">
            {:else if record.file && record.file.endsWith('.mp4')}
                <!-- Display videos -->
                <!-- svelte-ignore a11y-media-has-caption -->
                <video controls>
                    <source src={"https://system-giving.pockethost.io/api/files/ewwz4ryyhb4hv1n/" + record.id + "/" + record.file + "?token="} type="video/mp4">
                </video>
            {/if}
        </li>
    {/each}
</ul>
