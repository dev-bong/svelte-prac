<script>
    import fastapi from "../lib/api"
    let post_list = []
  
    function get_post_list() {
        fastapi('get', '/api/post/list', {}, (json) => {
            post_list = json
        })
    }
  
    get_post_list()
  </script>
  
  <ul>
    {#each post_list as post}
      <li>{post.subject}</li>
    {/each}
  </ul>