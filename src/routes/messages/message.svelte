<!-- Message.svelte -->
<script>
    import { createEventDispatcher } from 'svelte';
    import PocketBase from 'pocketbase';

    const pb = new PocketBase('https://system-giving.pockethost.io');
    const dispatch = createEventDispatcher();

    let messageText = ''; // Input field for message text
    const userId = '5vei3ncpfs8m15u'; // Hardcoded user ID
    let errorMessage = ''; // Error message display

    async function createMessage() {
        try {
            // Check if message text is provided
            if (!messageText.trim()) {
                errorMessage = 'Please enter message text';
                return;
            }

            const data = {
                "message": messageText,
                "user": userId
            };

            // Create the message record
            const record = await pb.collection('messages').create(data);

            // Dispatch event to indicate message creation
            dispatch('messageCreated');

            // Clear input field and error message
            messageText = '';
            errorMessage = '';
        } catch (error) {
            console.error('Error creating message:', error);
            errorMessage = 'Error creating message. Please try again later.';
        }
    }
</script>

<h2>Create Message</h2>

{#if errorMessage}
    <p style="color: red;">{errorMessage}</p>
{/if}

<div>
    <textarea id="messageText" bind:value={messageText}></textarea>
</div>

<button on:click={createMessage}>Create Message</button>
