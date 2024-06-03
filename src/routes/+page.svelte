
<script>
	
	import Sources from '$lib/components/Sources.svelte';
	
</script>


<!-- <div class='answer_canvas'>
	<div class='answers_wrap'>
		<div class='answer'>
			<div class='summary'>
				<span class='data_hole user_prompt'>What's the song with something like 'guy living in blue world' in it?</span>
				<div class='spacer_m'></div>
				<p class='data_hole answer_summary'>The song you're referring to is "Blue (Da Ba Dee)" by Eiffel 65</p>
				<div class='spacer_m'></div>
				<Sources />
			</div>
		</div>
	</div>
</div> -->


<style>
	
	.answer_canvas {
		width: calc(100% - 78px);
		margin-left: 78px;
		height: 100vh;
	}
	
	.answers_wrap {
		position: relative;
		top: 40%;
		transform: translateY(-50%);
	}
	
	.data_hole {
		border-radius: 12px;
	}
	
	.user_prompt {
		display: inline-block;
		min-width: 300px;
		min-height: 20px;
	}
	
	.answer_summary {
		min-width: 340px;
		min-height: 40px;
	}
	
	.user_prompt.loading, .answer_summary.loading {
		background-color: rgba(255, 255, 255, 0.05);
	}
	
	
	
	
</style>