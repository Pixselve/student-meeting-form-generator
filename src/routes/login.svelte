<form on:submit|preventDefault={login}>
	<input bind:value={email} type='email' placeholder='email'>
	<input bind:value={password} type='password' placeholder='password'>
	<button disabled={loading}>Login</button>
</form>

<script lang='ts'>
import { signInWithEmailAndPassword, getAuth } from 'firebase/auth';



let loading = false;
let email = '';
let password = '';

async function login() {
	const auth = getAuth();
	try {
		loading = true;
		const user = await signInWithEmailAndPassword(auth, email, password);
		console.log({user});
	} catch (e) {
		console.error({ loginError: e });
	} finally {
		loading = false;
	}
}
</script>
