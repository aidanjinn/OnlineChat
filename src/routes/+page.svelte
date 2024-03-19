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
        intervalId = setInterval(fetchRecords, 1000);
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

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

<!-- Render the fetched records -->
<ul>
    {#each records as record}
        <li>{record.message}</li> 
    {/each}
</ul>

<!-- Render Message component with event listener -->
<Message on:messageCreated={handleMessageCreated}/>
