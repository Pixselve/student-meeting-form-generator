<header class='mb-5 flex space-x-5'>
	<input type='text'
				 class='p-3 rounded-xl border w-full focus:ring-2 outline-none ring-blue-600 disabled:bg-gray-100 hidden'
				 placeholder='Rechercher'>
	<!-- spacer	-->
	<div class='w-full'></div>
	<a href='/new'
		class='transition ease-in-out hover:shadow-md h-14 w-14 bg-blue-600 text-white block rounded-xl flex justify-center items-center text-xl transform-gpu hover:scale-105'>
		<svg xmlns='http://www.w3.org/2000/svg' class='h-1/2 w-1/2' viewBox='0 0 20 20' fill='currentColor'>
			<path fill-rule='evenodd'
						d='M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z'
						clip-rule='evenodd' />
		</svg>
	</a>
</header>

<div class='space-y-5'>
	{#each reports as report}
		<StudentListItem report={report} />

	{/each}
</div>

<script lang='ts'>
import { onMount } from 'svelte';
import StudentListItem from '$lib/StudentListItem.svelte';
import { collection, getDocs, getFirestore } from 'firebase/firestore';

let reports = [];

onMount(async () => {
	const db = getFirestore();
	const querySnapshot = await getDocs(collection(db, 'reports'));
	reports = querySnapshot.docs.map(report => {
		const data = report.data();
		return data;
	});

});
</script>
