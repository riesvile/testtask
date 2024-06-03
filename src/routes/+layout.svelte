<script>
  import '../app.css'
  import '../typography.css'
  import Sources from '$lib/components/Sources.svelte';
  import { PUBLIC_OPENAI_API_KEY } from '$env/static/public';
  
  let key = PUBLIC_OPENAI_API_KEY;
  let imgkey = "AIzaSyCDI4ngygE6ziBa-R73TmMXCVpRL5tvSKU";
  let cx = "f6db0ab63501d4ca5";
  
  
  function model_response(prompt, format, response_key){
    fetch('https://api.openai.com/v1/chat/completions', {
      method: "POST",
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + key,
      },
      body: JSON.stringify(
        {
          "model": "gpt-4o",
          "messages": [{"role": "user", "content": prompt}],
          "response_format": { type: format },
        }
      )
    }).then(response => {
      return response.json();
    }).then(data => {
      try {
        console.log('maybe?');
        if (format == 'json_object'){
          response_obj[response_key] = JSON.parse(data.choices[0].message.content);
        } else {
          response_obj[response_key] = data.choices[0].message.content;
        }
        
        
        console.log(data.choices[0].message.content);
      } catch(e){
        console.log(e);
        model_response(prompt, format, response_key);
      }
      
    })
  }
  
  function get_summary(prompt){
    let string = "Give me a somewhat brief answer (1-2 sentences, try to keep it to one) to the following prompt: \n\n " + prompt;
    model_response(string, "text", "answer_summary");
  }
  
  function get_answer_blocks(prompt){
    let string = "You are a secondary assistant. User asks question and the primary assistant's job is to answer, but your job is to give me possible modules that could be useful to accompany the answer and populate them. For example, if a user were to ask something about a song, the possible modules could be 'Lyrics' and 'YouTube'. If a user asks for what's the population of Sweden, your answer could be an entity card. Limit it to 0-3 such modules per prompt and populate them either with table data or with a plain text string in the following JSON format: {'lyrics': {'format': 'string', 'content': 'complete lyrics'}, 'YouTube': {'format': 'list', 'content': ['headline 1', '13M views'], ['headline 2', '5M views']}}. Don't write any additional text whatsoever, only the json object. If you can't think of any module that would make sense, feel free to output an empty object, but this should be rare. \n\n The user's prompt is: " + prompt;
    model_response(string, "json_object", "blocks");
  }
  
  function get_sources(prompt){
    let string = "You are a very specific type of assistant. You don't answer questions, you only provide array of 2 to 4 URLs that you think might contain the answer to the user's prompt, but that's not all. In addition, you also provide a sensible headline and a 'source' excerpt - do your best guess for these strings. They don't need to be precise. Put the answer into a JSON representation like so: {1: ['website name', 'url (only the root, without any 'http' or whatnots - Just website.tld), 'text line 1 (headline)', 'text line 2 (description)'], 2: ... \n\n The user's prompt is: " + prompt;
    model_response(string, "json_object", "sources");
  }
  
  function populate_blocks(prompt, blocks){
  }
  
  function fetch_image(query){
    fetch("https://www.googleapis.com/customsearch/v1?key=" + imgkey + "&cx=" + cx + "&q=" + query + "&searchType=image", {
      headers: {
        "Content-Type": "application/json",
      }
    }).then(response => {
      return response.json();
    }).then(data => {
      try {
        let img_link = data.items[0].image.thumbnailLink;
        console.log(img_link);
        return img_link;
      } catch(e){
        console.log(e);
      }
    })
  }
  
  function generate_image(prompt){
    fetch('https://api.openai.com/v1/images/generations ', {
      method: "POST",
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + key,
      },
      body: JSON.stringify(
        {
          "model": "dall-e-2",
          "prompt": "a white siamese cat",
          "n": 1,
          "size": "1024x1024"
        }
      )
    }).then(response => {
      return response.json();
    }).then(data => {
      console.log(data)
      console.log('here?');
  })
  
}
  
  
  
  
  
  let main_open = false;
  let new_leaf_created = false;
  let answer_state =false;
  let placeholder_string = 'Ask anything...';
  let continue_placeholder_string = 'Follow-up question or action';
  let main_input_placeholder = 'Ask anything...';
  let main_input_val = '';
  let continue_input_placeholder = 'Follow-up question or action';
  let continue_input_val = '';
  
  let user_prompt = '';
  let response_obj = {
    answer_summary: '',
    blocks: {},
    sources: {}
  }
  
  
  function setCursorToEnd(element) {
      element.focus();  
      const range = document.createRange(); 
      const selection = window.getSelection();
      range.selectNodeContents(element); 
      range.collapse(false); 
      selection.removeAllRanges();
      selection.addRange(range);
  }
  
  function handle_input(e){
    let text = e.target.textContent;
    if (text === ''){
      main_input_placeholder = placeholder_string;
      continue_input_placeholder = continue_placeholder_string;
    } else {
      main_input_placeholder = '';
      continue_input_placeholder = '';
    }
    if (text == '\n') console.log('newline');
    // if (e.target.clientHeight > 35) leaf_creation();
  }
  
  function handle_keydown(e){
    if (e.key == 'Enter') {
      console.log('sent');
      main_input_val == '' ? e.preventDefault() : prompted(main_input_val);
      // continue_input_val == '' ? e.preventDefault() : prompted(continue_input_val);
    }
  }
  
  function prompted(prompt){
    answer_state = true;
    user_prompt = prompt;
    get_summary(main_input_val);
    get_answer_blocks(main_input_val);
    get_sources(main_input_val);
    main_input_val = '';
    setTimeout(() => {
      document.querySelector('.continue_input').focus();
    }, 100);
    // test_call('how are you');
    // generate_image('abc', '256x256');
    // fetch_image('sweden flag');
  }
  
</script>


<a href={'#'} class='xai_logo' on:click={() => {main_open = !main_open}}><!-- --></a>

<div class="main_input" class:answer_state contenteditable="true" bind:textContent={main_input_val} on:input={handle_input} on:keydown={handle_keydown}>{main_input_val}</div>
<div class='main_input_placeholder' class:answer_state>{main_input_placeholder}</div>

<div class='controls' class:answer_state>
  <div class='new_context'>
    <div class='new_input'>New context <span class='keyboard_shortcut'>Cmd+K</span></div>
  </div>
  <div class='continue' class:answer_state>
    <div class="continue_input" class:answer_state contenteditable="true" bind:textContent={main_input_val} on:input={handle_input} on:keydown={handle_keydown}>{main_input_val}
    </div>
    <div class='continue_input_placeholder'>{continue_input_placeholder}</div>
  </div>
  
</div>

<nav class='menu'>
  <a href={'#'}>Blog</a>
  <a href={'#'}>About</a>
  <a href={'#'}>Careers</a>
</nav>


<div class='answer_canvas' class:answer_state>
  <div class='answers_wrap'>
    <div class='answer'>
      <div class='summary'>
        <span class='data_hole user_prompt'>{user_prompt}</span>
        <div class='spacer_m'></div>
        <p class='data_hole answer_summary' class:loading={(response_obj['answer_summary'] == '')}>{response_obj['answer_summary']}</p>
        <div class='spacer_m'></div>
        <Sources bind:sources={response_obj.sources} />
      </div>
      <div class='blocks'>
        {#each Object.entries(response_obj['blocks']) as [block_name, val]}
        <div class='block'>
          <span class='block_name'>{block_name}</span>
          {#if val.format == 'string'}
            <p class='block_content_string'>{val.content}</p>
          {:else if val.format == 'list'}
            <div class='list_content'>
              <div class='list_item'>
                <img src='https://picsum.photos/112/60' class='thumbnail'>
                <p class='block_content_list_head'>{val.content[0]}</p>
              </div>
              <div class='spacer_m'></div>
              <div class='list_item'>
                <img src='https://picsum.photos/112/60' class='thumbnail'>
                <p class='block_content_list_head'>{val.content[0]}</p>
              </div>
            </div>
          {/if}
        </div>
        {/each}
      </div>
    </div>
  </div>
</div>


<slot />
  
  
<style>
  
  /* --- carried from +page.svelte for speed's sake. Modularization otherwise obviously ftw! --- */
  
  img.thumbnail {
    width: 112px;
    margin-left: 16px;
    max-height: 60px;
    float: left;
    border-radius: 12px;
    margin-right: 12px;
  }
  
  .block_content_list_head {
    padding-top: 0px !important;
  }
  
  .list_item {
    /* background-color: rgba(240, 32, 42, 0.3); */
    clear: both;
  }
  
  .list_content {
    padding-top: 40px;
    width: 100%;
  }
  
  .blocks {
    /* display: flex; */
    gap: 12px;
    position: relative;
    top: 60px;
  }
  
  .block {
    position: relative;
    background-color: rgba(215, 207, 230, 0.06);
    border: 1px solid rgba(215, 207, 230, 0.08);
    display: inline-block;
    width: 360px;
    min-height: 80px;
    border-radius: 16px;
    padding-bottom: 14px;
    float: left;
    margin-right: 12px;
  }
  
  .block_name {
    position: absolute;
    top: 16px;
    left: 20px;
    font-size: 12px;
    color: rgba(255, 255, 255, 0.56);
  }
  
  .block_content_string, .block_content_list_head {
    white-space: pre-wrap;
    font-size: 14px;
    line-height: 20px;
    color: rgba(255, 255, 255, 0.96);
    padding-top: 46px;
    width: calc(100% - 40px);
    margin-left: 20px;
  }
  
  .answer_canvas {
    width: calc(100% - 78px);
    margin-left: 78px;
    height: 100vh;
    opacity: 0;
    pointer-events: none;
  }
  
  .answer_canvas.answer_state {
    opacity: 1;
    pointer-events: auto;
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
  
  
  /* -- */
  
  
  .menu {
    font-size: 13px;
    position: fixed;
    top: 24px;
    right: 20px;
  }
  
  .menu a {
    text-decoration: none;
    color: rgba(215, 207, 230, 0.6);
    display: inline-block;
    padding: 4px 12px;
    transition: color 0.2s;
  }
  
  .menu a:hover {
    color: rgba(215, 207, 230, 0.8);
  }
  
  .main_input, .main_input_placeholder, .continue_input, .continue_input_placeholder {
    position: fixed;
    top: calc(50% - 32px);
    left: 78px;
    width: 400px;
    color: hsla(270, 44%, 96%, 0.86);
    font-family: Inter;
    font-size: 20px;
    font-weight: 400;
    outline: none;
    /* transform: translate(-10px, -10px); */
    opacity: 1;
    transition: 0.3s transform, 0.2s opacity;
  }
  
  .main_input {
    color: hsla(270, 44%, 96%, 0.96);
    opacity: 1;
  }
  
  .main_input.answer_state, .main_input_placeholder.answer_state {
    opacity: 0;
    pointer-events: none;
    transform: translateY(-40px);
  }
  
  .main_input_placeholder, .continue_input_placeholder {
    pointer-events: none;  
  }
  
  .controls {
    position: fixed;
    top: 16px;
    left: 78px;
    opacity: 0;
    transition: opacity 0.2s;
  }
  
  .controls.answer_state {
    opacity: 1;
    pointer-events: auto;
  }
  
  .new_context {
    font-size: 14px;
    line-height: 40px;
    height: 40px;
    background-color: rgba(215, 207, 230, 0.1);
    display: inline-block;
    padding: 0 14px 0 16px;
    color: rgba(215, 207, 230, 0.56);
    border-radius: 20px;
    float: left;
    cursor: pointer;
    transition: background-color 0.2s;
  }
  
  .new_context:hover {
    background-color: rgba(215, 207, 230, 0.16);
  }
  
  .new_input {
    
  }
  
  .continue {
    display: inline-block;
    height: 40px;
    background-color: red;
    position: relative;
    left: 16px;
  }
  
  .continue_input, .continue_input_placeholder {
    position: absolute;
    top: 0;
    left: 0;
    font-size: 14px;
    line-height: 40px;
  }
  
  .continue_input_placeholder {
    position: absolute;
    top: 0;
    left: 0;
  }
  
  .xai_logo {
    position: fixed;
    top: 20px;
    left: 20px;
    background: url(/assets/xai_logo.svg);
    background-size: cover;
    width: 32px;
    height: 32px;
    pointer-events: auto;
    z-index: 11;
  }
  
  .keyboard_shortcut {
    font-size: 11px;
    color: rgba(215, 207, 230, 0.86);
    line-height: 19px;
    height: 20px;
    background-color: rgba(215, 207, 230, 0.1);
    border: 1px solid rgba(215, 207, 230, 0.08);
    display: inline-block;
    padding: 0px 4px 0px 4px;
    border-radius: 5px;
    box-sizing: border-box;
    margin-left: 4px;
  }
  
  .main_mask {
    --f :100px; /* control the fading */
    -webkit-mask:
      radial-gradient(at 0 0,#000,#0000 70%) 100% 100%/var(--f) var(--f),
      radial-gradient(at 100% 0,#000,#0000 70%) 0% 100%/var(--f) var(--f),
      linear-gradient( 90deg,#000,#0000) 100% 0/var(--f) calc(100% - var(--f)),
      linear-gradient( 90deg,#0000,#000) 0px 0/var(--f) calc(100% - var(--f)),
      linear-gradient(180deg,#000,#0000) var(--f) 100%/calc(100% - 2 * var(--f)) var(--f),
      linear-gradient(#000 0 0) var(--f) 0/calc(100% - 2 * var(--f)) calc(100% - var(--f));
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
    background-color: hsla(270, 60%, 2%, 0.0);
    z-index: -1;
    transition: 0.2s backdrop-filter, 0.2s background-color;
  }
  
  
  /* ----- */
  
  .content_wrap {
    padding-top: 100px;
    width: 100%;
    height: 100%;
    /* background-color: green; */
    position: relative;
  }
  
  
  span {
    outline: none;
  }
  
  
</style>