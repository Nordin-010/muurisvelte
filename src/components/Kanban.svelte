<script>
	import tasksData from "../data/tasks";
	import Icon from "@iconify/svelte";
	import { onMount } from "svelte";
	import { afterUpdate } from "svelte";
	// @ts-ignore
	import Muuri from "muuri";
	import Card from "../components/Card.svelte";

	let gridList = [];
	let doneItems = [];
	let taskItems = [];
	let currentSprint = null;

	async function onGridChanged(id, item, index) {
		console.log("onGridChanged");
		const containerId = item.getGrid().getElement().id;

		console.log("Grid container ID: ", containerId);
		console.log("Taks ID: ", id);
		console.log("Item index: ", index);
	}

	onMount(() => {
		// 1. First we need to fetch all user stories
		console.log("Simulate data fetch");

		setTimeout((_) => {
			// grab tasks to render
			taskItems = tasksData;
		}, 2000);
	});

	// after rendering, this method is called
	afterUpdate(() => {
		console.log("afterUpdate()");

		// We need this lifecycle function to create Muuri objects
		// based on rendered data

		// first time taskItems is not loaded, so we do nothing
		if (!taskItems.length) return;

		//  if taskItems data is available, grab the todo container
		//  tell Muuri to use this element as container
		let selector = "grid-todo";
		let gridContainer = document.querySelector("#" + selector);

		// create Muuri instance for todo
		let gridTodo = createGrid(gridContainer, gridList);
		console.log("todo-col - grid created: ", gridTodo);
		gridList.push(gridTodo);

		// now grab the done container element and do the same as above
		selector = "grid-done";
		gridContainer = document.querySelector("#" + selector);

		let gridDone = createGrid(gridContainer, gridList);
		console.log("done-col - grid created: ", gridDone);
		gridList.push(gridDone);

		// This should be enough to drag items between two containers,
		// at least in theory.

		console.log("Grids: ", gridList);
	});

	function createGrid(content, list) {
		console.log("createGrid() ", content);
		const grid = new Muuri(content, {
			dragEnabled: true,
			dragPlaceholder: {
				enabled: true,
				/* The only way to get placeholder working */
				createElement(item) {
					const placeholder = document.createElement("div");
					placeholder.style.display = "block";
					placeholder.style.position = "absolute";
					placeholder.style.backgroundColor = "rgba(0, 0, 0, 0.1)";
					placeholder.style.border = "1px solid rgba(0, 0, 0, 0.2)";
					placeholder.style.borderRadius = "4px";
					placeholder.style.width = "100%";
					return placeholder;
				},
			},
			// @ts-ignore
			dragContainer: document.querySelector(".container"),
			// dragSort: function () {
			// 	return list;
			// },
		})
			.on("remove", function (items, indices) {
				console.log("onRemove item", items);
				console.log("onRemove indices", indices);

				// Update backend van een verwijderde object
				// dmv een id
			})
			.on("dragStart", function (item, event) {
				console.log("dragStart");

				const grid = item.getGrid();
				console.log("Grid id: ", grid._id);

				const index = grid.getItems().indexOf(item);
				console.log("Item index: ", index);

				const col = grid.getElement().id;
				console.log("Grid column: ", col);
				// @ts-ignore
				const state = item.gridState;
				if (state) {
					state.fromIndex = index;
					state.fromCol = col;
				}
			})
			.on("dragEnd", function (item, event) {
				console.log("dragEnd");

				const grid = item.getGrid();
				console.log("Grid: ", grid);
				const column = grid.getElement().id;

				const index = grid.getItems().indexOf(item);
				console.log("Column: ", column);
				console.log("Item index: ", index);
				// @ts-ignore
				const state = item.gridState;
				if (state) {
					state.triggerEvent(index, column);

					// Update backend van gewijzigde indexes
					console.log("Fired triggerEvent()");
				}
			})
			.on("layoutEnd", function (items) {
				console.log("layoutEnd", items);

				items.forEach((item) => {
					// @ts-ignore
					if (!item.gridState) {
						// attach gridstate
						const element = item.getElement();
						const id = element.dataset.id;
					}
				});
			});

		return grid;
	}

	// this is called when rendering is done
	afterUpdate(() => {
		console.log("afterUpdate: ", gridList);

		/**
		 * We need this implementation to rebuild the Muuri grid.
		 * If not, Muuri won't be noticed of added items.
		 */

		console.log("Grids: ", gridList);
	});
</script>

<div class="container is-flex">
	<div class="board__column">
		<div class="board__header" />
		<div class="grid-container">
			<div class="grid-header">
				TODO <button class="add-story"><Icon icon="mdi:plus-thick" /></button>
			</div>
			<div class="grid-content" id="grid-todo">
				{#each taskItems as task}
					<div class="item" data-id={task.id}>
						<div class="item-content">
							<!-- Safe zone, enter your custom markup -->
							<Card data={task} />
							<!-- Safe zone ends -->
						</div>
					</div>
				{/each}
			</div>
		</div>
	</div>
	<div class="board__column">
		<div class="board__header" />
		<div class="grid-container">
			<div class="grid-header">
				DONE {currentSprint && currentSprint.id >= 0 ? currentSprint.id : ""}
			</div>
			<div class="grid-content" id="grid-done">
				{#each doneItems as task}
					<div class="item" data-id={task.id}>
						<div class="item-content">
							<Card data={task} />
						</div>
					</div>
				{/each}
			</div>
		</div>
	</div>
</div>

<style>
	.board__column {
		width: 300px;
		margin: 1rem;
	}

	.board__header {
		min-height: 48px;
		max-height: 48px;
	}
	.grid-container {
		min-height: 128px;
		padding: 8px;
		background-color: honeydew;
		border-radius: 8px;
		border: 1px solid rgb(180, 189, 180);
		box-sizing: border-box;
	}
	.grid-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 4px;
		font-weight: bold;
		/* background-color: chartreuse; */
	}
	/* muuri styles */
	.grid-content {
		position: relative;
		/* background-color: deepskyblue; */
		box-sizing: border-box;
	}
	.item {
		display: block;
		position: absolute;
		width: 282px;
		z-index: 1;
		color: #fff;
		cursor: pointer;
		margin-bottom: 8px;
	}
	.item.muuri-item-dragging {
		z-index: 3;
	}
	.item.muuri-item-releasing {
		z-index: 2;
	}
	.item.muuri-item-hidden {
		z-index: 0;
	}
	.muuri-item-placeholder {
		z-index: 9;
		display: block;
		position: absolute;
		background-color: rgba(0, 0, 0, 0.1);
		border: 1px solid rgba(0, 0, 0, 0.2);
		border-radius: 8px;
		box-sizing: border-box;
		/* max-width: 100%; */
		width: 100%;
	}
	.item-content {
		position: relative;
		width: 100%;
		height: 100%;
	}
</style>
