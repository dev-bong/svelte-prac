<script>
  import Router from 'svelte-spa-router'
  import Home from "./routes/Home.svelte"
  import Detail from "./routes/Detail.svelte"
  import PostCreate from "./routes/PostCreate.svelte"
  import Navigation from "./components/Navigation.svelte"
  import UserCreate from "./routes/UserCreate.svelte"
  import UserLogin from "./routes/UserLogin.svelte"
  import PostModify from "./routes/PostModify.svelte"

  const routes = {
    '/': Home,
    '/detail/:post_id': Detail,
    '/post-create': PostCreate,
    '/user-create': UserCreate,
    '/user-login': UserLogin,
    '/post-modify/:post_id': PostModify,
  }
</script>

<Navigation />
<Router {routes}/>