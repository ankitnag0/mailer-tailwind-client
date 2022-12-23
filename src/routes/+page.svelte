<script>
    import rocket from '$lib/assets/Rocket.gif'
    import { io } from "socket.io-client";
    const socket = io("http://localhost:3000/");

    import axios from 'axios';
    let subject = "";
    let mailContent = "";

    let successRecipient = [];

    const getFiles = () => {
        const emailListInput = document.getElementById('emailList');
        const attachmentInput = document.getElementById('attachment');
        return {
            emailList: emailListInput?.files[0],
            attachment: attachmentInput?.files[0]
        };
    }

    let isLoading = false;

    const handleSubmit = async() => {
        isLoading = true;

        try {

            const formData = new FormData();
            formData.append('subject', subject);
            formData.append('mailContent', mailContent);

            const {emailList, attachment} = getFiles();
            formData.append('emailList', emailList); 
            formData.append('attachment', attachment);
            
            const response = await axios.post('http://localhost:3000/upload', formData);
            console.log(response.data); 

        } catch (error) {
            console.log(error);
        }
    }

    socket.on('connect', () => {
        console.log(socket.id);
    });

    socket.on('disconnect', () => {
        console.log('Disconnected');
    });

    socket.on('mailSuccess', ({email}) => {
        console.log(`Mail sent successfully to ${email}`);
        isLoading = false;
        successRecipient = [...successRecipient, email];
    })

</script>

<section class="p-5 bg-white text-black centet">
    
    <h1 class="text-center text-6xl text-blue-700 mb-5 font-bold">Mailer</h1>
    <div class="flex w-2/3 justify-evenly shadow-2xl rounded-xl mx-auto">
        <div class="w-2/5 p-5">
            <h3 class="text-center text-2xl font-bold">Form</h3>
            <form action="" on:submit|preventDefault={handleSubmit}>
                <div class="p-2 flex flex-col gap-1">
                    <label for="emailList">Email List </label>
                    <input class="custom-file-input" type="file" name="emailList" id="emailList">
                </div>
                <div class="p-2 flex flex-col gap-1">
                    <label for="subject">Subject </label>
                    <input class="custom-text-field" type="text" name="subject" id="subject" bind:value={subject}>
                </div>
                <div class="p-2 flex flex-col gap-1">
                    <label for="mailContent">Mail Content </label>
                    <input class="custom-text-field" type="text" name="mailContent" id="mailContent" bind:value={mailContent}>
                </div>
                <div class="p-2 flex flex-col gap-1">
                    <label for="attachment">Attachment </label>
                    <input class="custom-file-input" type="file" name="attachment" id="attachment" >
                </div>
                <div class="p-2 flex flex-col gap-1">
                    <input type="submit" value="Submit" class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center">
                </div>
            </form>
        </div>
        <div class="w-2/5 p-5 flex flex-col">
            <h3 class="text-center text-2xl font-bold">Success</h3>
            {#if isLoading}
                <div svelte:loading class="flex justify-center items-center flex-grow">
                    <img src={rocket} alt="rocket-loading">
                </div>
            {/if}
            <div>
                {#each successRecipient as email}
                    <div class="shadow-2xl p-2 m-2 rounded-lg">
                        <p class="text-center">{email}</p>
                    </div>
                {/each}
            </div>
        </div>
    </div>

</section>

<style>

</style>

