<template>
    
  <div class="chat-app">
    
    <canvas ref="canvas" style="display:none;"></canvas>
    <div class="app-container">
     
      <div class="chat-container">
        <div class="header">
      <h2>Product Catalog Database Assistant</h2>
    </div>
    <hr>
    <div class="chat-messages" ref="chatMessages">
      <!-- Messages will be displayed here -->
      <div v-for="message in messages" :key="message.id" class="message">
          <img v-if="message.type==='image'" :src="message.text" alt="Image"  class="image-uploaded"/>
          
        <div v-if="message.type==='text'" class="user-message"><b>{{ message.sender }}:</b>{{ message.text }} <a v-if="message.link" :href="message.link" target="_blank">Download</a></div>
        <div v-if="message.documents" class="ai-message"><b>{{ message.sender }}:</b>
          <div class="retrieved-documents">
          <div v-for="document in message.documents" :key="document"  class="" >
            <div class="assistant-message">{{document.assistant}}</div>
            <details open>
              <summary>Retrieved Document</summary>
              <div v-for="field in Object.keys(document)" :key="field" >
                <div v-if="field !== '_id' && field !== 'imgUrl' && field !== 'assistant'" class="field"><b>{{ field }} : </b>{{ document[field] }}</div>
              </div>
            </details>
            
          </div>
          </div>
        </div>
      </div>
      <div class="message" v-if="loading">
        <img class="ai-message loading-image" src="../assets/dribble-dots.gif" alt="Loading" />
      </div>
    </div>
    <div class="message-input">
      <!-- Text input for typing messages -->
      <textarea v-model="newMessage" placeholder="Type a message..."></textarea>
      <!-- Send button to send the message -->
      <div class="send-container" >
        <button id="sendButton" @click="sendMessage" title="Send Message">Send</button>
        <!-- File input hidden; click on ➕ button to trigger it -->
        <input type="file" ref="fileInput" @change="onFileChange" accept="image/jpeg, image/png, image/gif" style="display: none"  />
        <button @click="triggerFileInput" title="Image upload">📎</button>
       </div>
    </div>
    </div>
    <iframe :src="sideUrl" width="100%" height="100%" frameborder="0"></iframe>
    </div>
    <!-- PDF iframe display link : https://soundscout.s3.eu-central-1.amazonaws.com/The+CLUE.pdf-->
   
    
  </div>
</template>

<script>
import { ref, onMounted, onUpdated } from 'vue';
import axios from 'axios';
import confetti from 'canvas-confetti';

export default {
  props: {
    player: {
      type: Object,
      required: true
    }
  },
  setup(props) {

    //const  player  = props.player
    const messages = ref([
      {
        id: Date.now(), // Unique ID for the message
        text: `Welcome ${props.player.name}, I'm the investigation database assistant chatbot. Please Download and unzip your kit from the link here and start investigating. Remember to either message me or upload files using the attachment button below.`,
        link: "https://soundscout.s3.eu-central-1.amazonaws.com/evidence.zip",
        type : 'text',
        sender: 'AI',
      },
    ]); // Store the messages
    const newMessage = ref(''); // The new message to send
    const fileInput = ref(null); // Reference to the file input element
    let medalAward = false;
    let loading = ref(false);
    let sideUrl=ref(process.env.VUE_APP_SIDE_IFRAME)
    const chatMessages = ref(null);

    const scrollToBottom = () => {
      if (chatMessages.value) {
        chatMessages.value.scrollTop = chatMessages.value.scrollHeight;
      }
    };
    
    const playConfetti = () => {
    const duration = 30 * 1000; // Duration of the confetti effect in milliseconds

    confetti({
      particleCount: 100, // Number of confetti particles
      spread: 160, // Spread of confetti particles
      origin: { y: 0.6 }, // Starting point of confetti particles (from the top)
      colors: [`#13aa52`, `#4caf50`, `#007b1d`, `#ff6600`, `#ff4400`, `#ff9900`] // Colors of the confetti particles
    });

    

    // Stop the confetti effect after the specified duration
    setTimeout(() => {
      confetti.reset();
    }, duration);
  }

  const disableAllButtons = () => {

    const buttons = document.querySelectorAll('button');
    buttons.forEach(button => {
      button.disabled = true;
    });

  }

  const enableAllButtons = () => {

    const buttons = document.querySelectorAll('button');
    buttons.forEach(button => {
      button.disabled = false;
    });

   
  }
    // Function to send a new message
    const sendMessage = async () => {
      disableAllButtons();
      const inputMsg = newMessage.value.trim();
      newMessage.value = '';
      try
      {
        if (medalAward) {
        
        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: inputMsg, // The message text
          type : 'text',
          sender: 'User'
        });
        loading.value = true;
        await generateMedal(inputMsg);
        loading.value = false;
        enableAllButtons();
        return;

      }
         
      if (inputMsg) {
        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: inputMsg, // The message text
          type : 'text',
          date : Date.now(),
          sender: 'User'
        });
        loading.value = true;
        console.log('VUE_APP_BASE_APP_SERVICE_URL', process.env.VUE_APP_BASE_APP_SERVICE_URL)
        console.log('VUE_APP_SEARCH_ENDPOINT', process.env.VUE_APP_SEARCH_ENDPOINT)
        const response = await axios.post(
         `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_SEARCH_ENDPOINT}`,
          { text: inputMsg , player : props.player.name},
          { headers: { 'Content-Type': 'application/json' } }
        );
        // Add a message indicating the image was sent
        console.log('response', response.data);
        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: (response.data[0].imgUrl) ? `${response.data[0].imgUrl}` : '' ,// The message text
          documents: response.data,
          type: (response.data[0].imgUrl) ? 'image' : 'json',
          sender: 'AI'
        });
      

        if (response.data[0]['finish']) {
          medalAward = true;
          messages.value.push({
            id: Date.now(), // Unique ID for the message
            text: `Congratulations ${props.player.name}, you have completed the investigation.\n\n You can prompt me to generate a medal based on your descirption for you ... ;)`, // The message text
            type : 'text',
            sender: 'AI'
          });
          //disable send button


          playConfetti();
        }
        loading.value = false;
        await axios.post(
          `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_SAVE_CHATS_ENDPOINT}`,
          { player : props.player.name, messages: messages.value },
          { headers: { 'Content-Type': 'application/json' } }
        );
        
      }
      enableAllButtons();

    } catch (error) {
      enableAllButtons();
      loading.value = false;
      console.error('Error uploading message:', error);
      messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: 'Error sending message... ' + error.message, // The message text
          date : Date.now(),
          type : 'text',
          sender: 'AI'
        });
      }
    };

    const generateMedal = async (msgText) => {
      messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: `Generating medal for ${props.player.name}...`, // The message text
          type : 'text',
          sender: 'AI'
        });

        const medalResponse = await axios.post(
          `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_MEDAL_ENDPOINT}`,
          { player : props.player.name, text: msgText },
          { headers: { 'Content-Type': 'application/json' } }
        );

        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: `data:image/png;base64,${medalResponse.data.images[0]}`, // The message text
          date : Date.now(),
          type : 'image',
          sender: 'AI'
        });

        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: `Yey! You can type again for a different medal...`, // The message text
          type : 'text',
          sender: 'AI'
        });

        await axios.post(
         `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_SAVE_CHATS_ENDPOINT}`,
          { player : props.player.name, messages: messages.value },
          { headers: { 'Content-Type': 'application/json' } }
        );
    }

    // Function to trigger the file input
    const triggerFileInput = () => {
      fileInput.value.click();
    };


    // Function to handle file changes
    const onFileChange = (e) => {
      try{
      
      const file = e.target.files[0];
      if (file) {
        // Convert to base64 and send the image
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => {
          const base64Content = reader.result.split(',')[1];
          sendImage(base64Content);
        };
      }
      // clear input
      e.target.value = '';
    } catch (error) {
      console.error('Error uploading image:', error);
      messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: 'Error uploading image...', // The message text
          type : 'text',
          sender: 'AI'
        });
      }
    };



    const imageAnalysisEffect = async () =>  {
      
      messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: 'Uploading image...', // The message text
          type : 'text',
          sender: 'AI'
        });


    }

    // Function to send the image to the server
    const sendImage = async (base64) => {
      try {
        loading.value = true;
        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: `data:image/png;base64,${base64}`, // The message text
          type : 'image',
          sender: 'User'
        });
        imageAnalysisEffect();
        const response = await axios.post(
         `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_SEARCH_ENDPOINT}`,
          { image: base64, player : props.player.name },
          { headers: { 'Content-Type': 'application/json' } }
        );
        console.log('Image uploaded successfully:', response.data);
        // Add a message indicating the image was sent
        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: `Found:` ,// The message text
          documents: response.data,
          type: 'json',
          sender: 'AI'
        });
        loading.value = false;
        await axios.post(
          `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_SAVE_CHATS_ENDPOINT}`,
          { player : props.player.name, messages: messages.value },
          { headers: { 'Content-Type': 'application/json' } }
        );
        
      } catch (error) {
        loading.value = false;
        enableAllButtons();
        console.error('Error uploading image:', error);
        messages.value.push({
          id: Date.now(), // Unique ID for the message
          text: 'Error uploading image...', // The message text
          type : 'text',
          sender: 'AI'
        });
      }
    }
    
    onMounted(async () => {
      const messageResponse = await axios.get(
        `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_GET_CHATS_ENDPOINT}?player=${props.player.name}`,
        { headers: { 'Content-Type': 'application/json' } }
      );
      if (messageResponse.data.messages && messageResponse.data.messages.length > 0)
        messages.value = messageResponse.data.messages;

    });

    onUpdated(scrollToBottom);

    return {
      messages,
      sideUrl,
      chatMessages,
      newMessage,
      loading,
      sendMessage,
      triggerFileInput,
      onFileChange,
      fileInput

    };
  },
};
</script>

<style scoped>
.chat-app {
  display: flex;
  flex-direction: column;
  height: 95vh;
  max-width: 1400px;
  margin: auto;

}

.loading-image {
  width: 500px;
  height: 300px;
  object-fit: contain;
}

.chat-messages {
  overflow-y: auto;
  padding: 10px;
  flex-grow: 1;
}
.app-container {
  display: flex;
  flex-direction: row;
  height: 100%;
}

.chat-container {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  height: 100%;
  border: 1px solid #ddd;
  background-color: #f9f9f9;
}

.message {
  margin-bottom: 10px;
  padding: 10px;
  background-color: #fff;
  max-width: 1400px;
  flex-wrap: wrap;
  border-radius: 10px;
  box-shadow: 0 1px 1px rgba(0,0,0,0.1);
}

.user-message, .ai-message {
  display: flex;

gap: 10px;
justify-content: flex-start;
}

.json-message {
  display: flex;
  flex-direction: column;
  gap: 10px;
  justify-content: flex-start;
}
/* .header {
  padding: 20px;
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
}

.header-img {
  width: 100vw;
  max-width: 200px;
  height: auto;
  object-fit: contain;
} */


.message-input {
  display: flex;
  padding: 10px;
  background: #fff;
  border-top: 1px solid #ddd;
}

.retrieved-documents{
  display: flex;
  flex-direction: column;
  gap: 10px;
  justify-content: flex-start;
}

.image-uploaded {
  width: 100%;
  height: 200px;
  object-fit: contain;
}
.send-container {
  display: flex;
  gap: 10px;
}
textarea {
  flex-grow: 1;
  padding: 10px;
  margin-right: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  resize: none;
}

@media (max-width: 600px) {
  .app-container {
    flex-direction: column;
  }
}

button {
  padding: 10px 15px;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

button:focus {
  outline: none;
}

input[type='file'] {
  display: none;
}

.chat-messages::-webkit-scrollbar {
  width: 5px;
}

.chat-messages::-webkit-scrollbar-track {
  background: #f1f1f1;
}

.chat-messages::-webkit-scrollbar-thumb {
  background: #888;
}

.chat-messages::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>

