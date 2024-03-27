<script>
	let name = '';
</script>

<div class='container'>
	<div class="innerCtn">
		<input class="inp" bind:value={name} placeholder="enter your name" />
		<h1>Hello {name}!</h1>
		<button on:click={() => { name = ''}}>清空</button>
	</div>
</div>

<style lang="scss">
.container {
	padding: 20px;
	border: 1px solid red;
	.innerCtn {
		padding: 20px;
		border: 1px solid gray;
		.inp {
			padding: 5px 10px;
		}
	}
}
</style>
