<script>
    import fastapi from "../lib/api"

    export let params = {}
    let post_id = params.post_id
    
    let post = {}

    function get_post() {
        fastapi("get", "/api/post/detail/" + post_id, {}, (json) => {
            post = json
        })
    }

    get_post()
</script>

<h1>{post.subject}</h1>
<div>
    {post.content}
</div>