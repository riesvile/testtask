<script>
	
	let sources_open = false;
	let chip_string = '3 sources';
	let loading = true;
	
	export let sources = {};
	
	$: if (Object.keys(sources).length != 0) loading = false;
	
</script>


<div class='sources_wrap'>
	<div class='sources_chip' class:loading on:click={() => sources_open = !sources_open}>{sources_open ? 'Close' : chip_string}</div>
	<div class='sources_list' class:sources_open>
		<div class='sources_overlay main_mask masked' class:sources_open></div>
		{#each Object.entries(sources) as [i, vals]}
			<div class='source'>
				<img src={'https://icon.horse/icon/' + vals[1]} class='s_favicon'>
				<span class='source_name'>{vals[0]}</span>
				<p class='source_line1'>{vals[2]}</p>
				<div class='spacer_xs'></div>
				<p class='source_line2'>{vals[3]}</p>
			</div>
		{/each}
		<!-- <div class='source'>
			<img src='https://www.songtexte.com/icons/favicon-32x32.png' class='s_favicon'>
			<span class='source_name'>songtexte</span>
			<p class='source_line1'>Blue (da ba dee) Lyrics – Eiffel 65 – Songtexte</p>
			<div class='spacer_xs'></div>
			<p class='source_line2'>Yo listen up, here's the story About a little guy that lives in a blue world And all day and all ...</p>
		</div>
		<div class='source'>
			<img src='https://www.songtexte.com/icons/favicon-32x32.png' class='s_favicon'>
			<span class='source_name'>songtexte</span>
			<p class='source_line1'>Blue (da ba dee) Lyrics – Eiffel 65 – Songtexte</p>
			<div class='spacer_xs'></div>
			<p class='source_line2'>Yo listen up, here's the story About a little guy that lives in a blue world And all day and all ...</p>
		</div> -->
	</div>
	
</div>


<style>
	
	.loading {
		opacity: 0;
	}
	
	.sources_overlay.sources_open {
		backdrop-filter: blur(30px) !important;
		background-color: rgba(34, 34, 34, 0.1) !important;
		transition: 0.2s backdrop-filter, 0.2s background-color;
	}
	
	.sources_list.sources_open .source{
		opacity: 1;
	}
	
	.sources_list.sources_open {
		pointer-events: auto !important;
	}
	
	.main_mask {
		--f: 80px; /* control the fading */
		-webkit-mask:
		  radial-gradient(at 0 100%,#000,#0000 70%) 100% 0/var(--f) var(--f),
			  radial-gradient(at 100% 100%,#000,#0000 70%) 0 0/var(--f) var(--f),
			  radial-gradient(at 0 0,#000,#0000 70%) 100% 100%/var(--f) var(--f),
			  radial-gradient(at 100% 0,#000,#0000 70%) 0% 100%/var(--f) var(--f),
			  linear-gradient(  0deg,#000,#0000) var(--f) 0%/calc(100% - 2 * var(--f)) var(--f),
		  linear-gradient( 90deg,#000,#0000) 100% var(--f)/var(--f) calc(100% - 2 * var(--f)),
			  linear-gradient( 90deg,#0000,#000) 0 var(--f)/var(--f) calc(100% - 2 * var(--f)),
		  linear-gradient(180deg,#000,#0000) var(--f) 100%/calc(100% - 2 * var(--f)) var(--f),
			  linear-gradient(#000 0 0) var(--f) var(--f)/calc(100% - 2 * var(--f)) calc(100% - 2 * var(--f));
		-webkit-mask-repeat: no-repeat;
	  }
	  
	  .masked {
		--offset: 100px;
		opacity: 1;
		width: calc(100% + 2 * var(--offset));
		height: calc(100% + var(--offset));
		margin-left: calc(var(--offset) * -1);
		/* transform: translate3d(var(--offset),var(--offset),0); */
		position: absolute;		
		top: 0;
		left: 0;
		backdrop-filter: blur(0px);
		background-color: rgba(34, 34, 34, 0.0);
		z-index: -1;
		transition: 0.2s backdrop-filter, 0.2s background-color;
	  }
	
	.sources_wrap {
		position: relative;
		width: auto;
	}
	
	.sources_list {
		position: absolute;
		top: 32px;
		left: 0;
		display: inline-block;
		z-index: 2;
		transition: opacity 0.2s;
		pointer-events: none;
	}
	
	.sources_overlay {
		position: absolute;
		top: -120px;
		left: -0px;
		width: calc(100% + 240px);
		height: calc(100% + 240px);
		background-color: rgba(34, 34, 34, 0.0);
		pointer-events: none;
		z-index: 1;
		transition: backdrop-filter 0.2s, background-color 0.2s;
	}
	
	.sources_chip {
		position: relative;
		height: 28px;
		line-height: 28px;
		background-color: rgba(215, 207, 230, 0.12);
		color: rgba(215, 207, 230, 0.56);
		padding: 0 12px;
		border-radius: 28px;
		display: inline-block;
		font-size: 12px;
		cursor: pointer;
		transition: background-color 0.2s, color 0.2s;
		z-index: 3;
	}
	
	.sources_chip:hover {
		background-color: rgba(215, 207, 230, 0.2);
		color: rgba(215, 207, 230, 0.68);
	}
	
	.source {
		position: relative;
		width: 232px;
		min-height: 120px;
		background-color: rgba(215, 207, 230, 0.08);
		border-radius: 16px;
		padding-bottom: 14px;
		transition: background-color 0.2s;
		cursor: pointer;
		display: inline-block;
		margin-right: 8px;
		z-index: 2;
		opacity: 0;
		float: left;
	}
	
	.source:hover {
		background-color: rgba(215, 207, 230, 0.12);
	}
	
	img.s_favicon {
		position: absolute;
		top: 16px;
		left: 16px;
		width: 18px;
		height: 18px;
		border-radius: 18px;
	}
	
	.source_name {
		font-size: 12px;
		color: rgba(255, 255, 255, 0.68);
		line-height: 18px;
		position: absolute;
		top: 16px;
		left: 42px;
	}
	
	.source_line1, .source_line2 {
		font-size: 13px;
		line-height: 18px;
		margin-left: 16px;
		width: calc(100% - 32px);
	}
	
	.source_line1 {
		color: rgba(255, 255, 255, 0.96);
		padding-top: 46px;
	}
	
	.source_line2 {
		color: rgba(255, 255, 255, 0.68);
		
	}
	
</style>
