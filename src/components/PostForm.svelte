<script>
 import {createEventDispatcher} from 'svelte';
   export let updating;

   const apiUrl = 'https://ndb99xkpdk.execute-api.eu-west-2.amazonaws.com/dev';
   const dispatch = createEventDispatcher();
   $:  title = updating.title;
   $:  body = updating.body;
   let loading = false;

   async function onSubmit(e){
       e.preventDefault();
       if(title.trim() === '' || body.trim()=== ''){
           return;
       }
       loading = true;
       const newPost = {title, body};
       let url, method;

       if(updating.id){
           url = `${apiUrl}/post/${updating.id}`;
           method = 'PUT'
       }
       else{
           url = `${apiUrl}/post`,
           method = 'POST'
       }

       const res = await fetch(url, {
           method,
           body: JSON.stringify(newPost)
       });
       const post = await res.json();
       //dispatch and reset form
       dispatch('postCreated', post);
       loading = false;
   }
</script>

<style>
  form{
      margin: 50px;
  }
  .progress{
      margin: 100px 0;
  }
</style>

{#if !loading}
    <form on:submit={onSubmit}>
        <div class="input-field">
            <label for="title">Title</label>
            <input type="text" bind:value={updating.title}/>
        </div>
        <div class="input-field">
            <label for="body">Body</label>
            <input type="text" bind:value={updating.body}/>
        </div>
        <button type="submit" class="waves-effect waves-light btn">
          {updating.id ? "Update": "Add"}
        </button>
    </form>
{:else}
   <div class="progress">
     <div class="indeterminate"></div>
   </div>
{/if}
