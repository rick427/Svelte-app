<script>
  import {onMount} from 'svelte';
  import PostForm from '../components/PostForm.svelte';

  const apiUrl = 'https://ndb99xkpdk.execute-api.eu-west-2.amazonaws.com/dev';
  let posts = [];
  let updating = {
      body: '',
      title: '',
      id: null
  }
  let postLimit = 3;

  onMount(async () => {
      const res = await fetch(apiUrl + '/posts');
      posts = await res.json();
  });

  function addPost({detail: post}){
      if(posts.find(p => p.id === post.id)){
          const index = posts.findIndex(p => p.id === post.id);
          let postsUpdated = posts;
          postsUpdated.splice(index, 1, post);
          posts = postsUpdated;
      }
      else{
          post = [post, ...posts];
      }
     updating = {
        body: '',
        title: '',
        id: null
      }
  }

  function editPost(post){
      updating = post
  }

  function deletePost(id){
      if(confirm("Are you sure you want to delete this")){
          fetch(`${apiUrl}/post/${id}`, {
          method: 'DELETE'
        })
        .then(res => {
            return res.json()
        })
        .then(() => {
            posts = posts.filter(post => post.id !== id)
          })
        }
  }

  function setLimit(){
      fetch(`${apiUrl}/posts/${postLimit}`).then(res => res.json())
        .then(postData => {
            posts = postData
        })
  }
</script>

<style>
  .delete-btn{
      color: red !important;
  }
  .card .card-content .card-title{
      margin-bottom: 0;
  }

  .card .card-content p.timestamp{
      color: #999;
      margin-bottom: 10px;
  }
</style>

<div class="row">
  <div class="col s6">
     <PostForm on:postCreated={addPost} {updating}/>
  </div>
  <div class="col s3" style="margin: 50px">
    <p>Limit number of posts</p>
    <input type="number" bind:value={postLimit}>
    <button on:click={setLimit} class="waves-effect waves-light btn">Set</button>
  </div>
</div>

<div class="row">
  {#if posts.length === 0}
    <h3>Loading Posts...</h3>
  {:else}
    {#each posts as post}
        <div class="col s6">
          <div class="card">
             <div class="card-content">
                 <p class="card-title">{post.title}</p>
                 <p class="timestamp">{post.createdAt}</p>
                 <p>{post.body}</p>
             </div>

             <div class="card-action">
               <a href="#;" on:click={() => editPost(post)}>Edit</a>
               <a href="#;" on:click={() => deletePost(post.id)} class="delete-btn">Delete</a>
             </div>
          </div>
        </div>
    {/each}
    {/if}
</div>