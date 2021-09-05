<script lang="ts">
import "../app.postcss";
import { initializeApp } from 'firebase/app';

// Your web app's Firebase configuration
const firebaseConfig = {
    apiKey: 'AIzaSyBzXNeviZuew8WwwYHJ0mFtAXqSDFIl95Y',
    authDomain: 'rendezvousform.firebaseapp.com',
    projectId: 'rendezvousform',
    storageBucket: 'rendezvousform.appspot.com',
    messagingSenderId: '842794282928',
    appId: '1:842794282928:web:1c9b21ab2ea00903403075'
};

initializeApp(firebaseConfig);


</script>

<div class='p-6 bg-gray-100 min-h-screen'>
	<slot></slot>
</div>


