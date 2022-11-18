<script>
  import Router from 'svelte-spa-router'
  import Home from "./routes/Home.svelte"
  import Detail from "./routes/Detail.svelte"
  import PostCreate from "./routes/PostCreate.svelte"

  const routes = {
    '/': Home,
    '/detail/:post_id': Detail,
    '/post-create': PostCreate,
  }
</script>

<Router {routes}/>