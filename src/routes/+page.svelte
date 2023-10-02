<!-- App.svelte -->
<script>
  import { onMount } from 'svelte';
  import Pusher from 'pusher-js';

  let messages = [];
  let newMessage = '';
  let userName = '';

  // Initialize Pusher
  const pusher = new Pusher("0448764325c710daa90f", {
    cluster: "ap2",
  });

  // Subscribe to the 'chat-channel'
  const channel = pusher.subscribe('chat-channel');

  // Listen for incoming messages on the channel
  channel.bind('message', (data) => {
    console.log('Received Message:', data);
    messages = [...messages, data];
  });

  // Scroll to the bottom of the message list when a new message arrives
  onMount(() => {
    const messageList = document.getElementById('message-list');
    messageList.scrollTop = messageList.scrollHeight;
  });

  // Handle sending a new message
  function sendMessage() {
    if (userName === '') {
      alert('Please enter your name before sending a message.');
      return;
    }

    const messageData = {
      user: userName,
      message: newMessage,
    };

    console.log('Sending Message:', messageData);

    messages = [...messages, messageData];

    channel.trigger('client-message', messageData);
    newMessage = '';
  }
</script>

<div>
  <div id="message-list" style="height: 200px; overflow-y: scroll;">
    <ul>
      {#each messages as message, index (index)}
        <li>{message.user}: {message.message}</li>
      {/each}
    </ul>
  </div>
  <input bind:value={newMessage} />
  <input bind:value={userName} placeholder="Your Name" />
  <button on:click={sendMessage}>Send</button>
</div>
