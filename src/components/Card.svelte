<script>
	import { createEventDispatcher } from "svelte";

	export let data;
	console.log("Story in card: ", data);

	const dispatch = createEventDispatcher();

	let clicked = false;
	function hanldeClick(e) {
		e.preventDefault();
		if (clicked) dispatch("data", data);

		clicked = false;
	}
</script>

<div
	class="board__card"
	on:mousedown={(e) => (clicked = true)}
	on:mousemove={(e) => (clicked = false)}
	on:mouseup={(e) => hanldeClick(e)}
>
	<div class="board__card-body">
		<div
			class="board__card-header"
			style="background-color: {data && data.category.color};"
		>
			{data && data.category.name}
		</div>
		<div class="board__card-content">
			<div class="board__card-title">
				<p class="truncate-single">{data && data.title}</p>
			</div>
			<div class="board__card-description truncate-multi">
				{data && data.description}
			</div>
			<div class="board__card-footer" />
		</div>
	</div>
</div>

<style>
	.board__card {
		max-height: 128px;
		border-radius: 4px;
		-webkit-box-shadow: 0px 0px 16px -4px rgba(0, 0, 0, 0.5);
		-moz-box-shadow: 0px 0px 16px -4px rgba(0, 0, 0, 0.5);
		box-shadow: 0px 0px 16px -4px rgba(0, 0, 0, 0.5);
		box-sizing: border-box;
		overflow: hidden;
	}
	.truncate-single {
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1; /* number of lines to show */
		line-clamp: 1;
		-webkit-box-orient: vertical;
		text-align: start;
		/* font-size: 0.9em; */
	}
	.truncate-multi {
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2; /* number of lines to show */
		line-clamp: 2;
		-webkit-box-orient: vertical;
		text-align: start;
		font-size: 0.9em;
	}

	.board__card-body {
		display: flex;
		flex-direction: column;
		text-align: center;
		box-sizing: border-box;
	}
	.board__card-header {
		width: 100%;
		height: 20px;
		background-color: slateblue;
		color: white;
		text-align: start;
		font-weight: bold;
		font-size: 0.8em;
		padding-left: 4px;
	}
	.board__card-content {
		width: 100%;
		padding: 8px 10px 4px 10px;
		background: white;
		color: black;
		box-sizing: border-box;
	}
	.board__card-title {
		padding-bottom: 4px;
		text-align: left;
		font-weight: 500;
		overflow-wrap: break-word;
	}
	.board__card-footer {
		display: flex;
		justify-content: space-between;
		align-items: center;
		border-top: 1px solid gray;
		font-size: 12px;
		font-style: italic;
		color: #aeaeae;
		text-align: right;
		box-sizing: border-box;
		padding-top: 2px;
		padding-bottom: 2px;
	}
</style>
