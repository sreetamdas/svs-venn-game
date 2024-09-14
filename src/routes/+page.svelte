<script>
	import { onDestroy, onMount } from 'svelte';

	/**
	 * @type {string[]}
	 */
	let hovered_circles = $state([]);
	/**
	 * @type {string[]}
	 */
	let all_circles = $state([]);

	/**
	 * @type {Record<string, Element | null> | null}
	 */
	let circles;
	/**
	 * @type {HTMLDivElement | null}
	 */
	let circles_container;

	/**
	 * @param {number} x
	 * @param {number} y
	 */
	function getElementFromPoint(x, y) {
		return document
			.elementsFromPoint(x, y)
			.map((el) => el.id)
			.filter((id) => id !== '')
			.toSorted();
	}

	/**
	 * @param {MouseEvent} event
	 */
	function mouseMoveListener(event) {
		const hovered = getElementFromPoint(event.clientX, event.clientY);

		if (
			hovered.length !== hovered_circles.length ||
			hovered.some((el, i) => el !== hovered_circles[i])
		) {
			hovered_circles = [...hovered];
		}
	}

	$effect(() => {
		all_circles.forEach((circle) => {
			if (hovered_circles.includes(circle)) {
				circles?.[circle]?.classList.add('active');
			} else {
				circles?.[circle]?.classList.remove('active');
			}
		});
	});

	onMount(() => {
		circles_container?.addEventListener('mousemove', mouseMoveListener);
		circles_container?.addEventListener('mouseleave', mouseMoveListener);
		let circle_elements = document.getElementsByClassName('venn-circle');

		circles = [...Array(3).keys()].reduce(
			(map, i) =>
				Object.assign(map, { [`area-${i + 1}`]: circle_elements.namedItem(`area-${i + 1}`) }),
			{}
		);
		all_circles = Object.keys(circles);
		console.log({ circles });
	});

	onDestroy(() => {
		circles_container?.removeEventListener('mousemove', mouseMoveListener);
		circles_container?.removeEventListener('mouseleave', mouseMoveListener);
	});

	$inspect(hovered_circles);
</script>

<div>
	<h2>word list</h2>
	<ul>
		<li>hello</li>
		<li>world</li>
		<li>javscript</li>
		<li>canvas</li>
		<li>game</li>
	</ul>
</div>

<div class="circles" bind:this={circles_container} id="container">
	<div id="area-1" class="venn-circle"></div>
	<div id="area-2" class="venn-circle"></div>
	<div id="area-3" class="venn-circle"></div>
</div>

<style lang="scss">
	.circles {
		position: relative;
		margin-top: 100px;
		min-height: 700px;
	}

	.venn-circle {
		position: absolute;
		z-index: 1;

		border-radius: 50%;
		border: 2px solid;
		min-height: 400px;
		min-width: 400px;

		pointer-events: auto;

		:global(&.active) {
			background-color: color-mix(in srgb, currentColor 20%, transparent);
		}
	}

	#area-1 {
		top: 0;
		left: 0;
		color: red;
		border-color: red;
	}
	#area-2 {
		top: 0;
		left: 250px;
		color: green;
		border-color: green;
	}
	#area-3 {
		top: 125px;
		left: 125px;
		color: blue;
		border-color: blue;
	}
</style>
