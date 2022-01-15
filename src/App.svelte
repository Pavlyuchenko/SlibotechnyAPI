<script>
	import { onMount } from "svelte";

	import { Router, Link, Route } from "svelte-routing";

	import Home from "./routes/Home.svelte";
	import BodyProgramu from "./routes/BodyProgramu.svelte";
	import NovyBod from "./routes/NovyBod.svelte";
	import UpravitBod from "./routes/UpravitBod.svelte";
	import NapsatClanek from "./routes/NapsatClanek.svelte";

	export let url = "";

	const resizeObserver = new ResizeObserver(draw);
	resizeObserver.observe(document.body);

	function draw() {
		function createBackground(rectWidth, width, height) {
			ctx.fillStyle = "#202020";
			for (let i = 0; i < width; i += 13) {
				for (let j = 0; j < height; j += 13) {
					ctx.fillRect(i, j, rectWidth, rectWidth);
				}
			}
		}

		function setCanvasDimensions(ctx) {
			var body = document.body,
				html = document.documentElement;
			var height = Math.max(
				body.scrollHeight,
				body.offsetHeight,
				html.clientHeight,
				html.scrollHeight,
				html.offsetHeight
			);
			var width = window.innerWidth;
			ctx.canvas.width = width;
			ctx.canvas.height = height;

			return [ctx, width, height];
		}

		var canvas = document.getElementById("canvas");
		if (canvas.getContext) {
			var ctx = canvas.getContext("2d");

			var canvasWidth;
			var canvasHeight;
			var rectWidth = 2;
			[ctx, canvasWidth, canvasHeight] = setCanvasDimensions(ctx);
			createBackground(rectWidth, canvasWidth, canvasHeight);
		}
	}

	onMount(draw);
</script>

<canvas id="canvas" />
<Router {url}>
	<Route path="/">
		<Home />
	</Route>
	<Route path="/strana/:str" let:params>
		<BodyProgramu strana={params.str} />
	</Route>
	<Route path="/novy-bod/:str" let:params>
		<NovyBod strana={params.str} />
	</Route>
	<Route path="/upravit-bod/:id" let:params>
		<UpravitBod id={params.id} />
	</Route>
	<Route path="/napsat-clanek">
		<NapsatClanek />
	</Route>
</Router>

<style>
	canvas {
		position: absolute;
		z-index: -1;
	}
</style>
