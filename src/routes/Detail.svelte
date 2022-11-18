<script>
    import fastapi from "../lib/api"

    export let params = {}
    let post_id = params.post_id
    let post = {comments:[]}
    let content = ""

    function get_post() {
        fastapi("get", "/api/post/detail/" + post_id, {}, (json) => {
            post = json
        })
    }

    get_post()

    function post_comment(event) {
        event.preventDefault()
        let url = "/api/comment/create/" + post_id
        let params = {
            content: content
        }
        fastapi('post', url, params, 
            (json) => {
                content = ''
                get_post()
            }
        )
    }
</script>

<h1>{post.subject}</h1>
<div>
    {post.content}
</div>
<ul>
    {#each post.comments as comment}
        <li>{comment.content}</li>
    {/each}
</ul>
<form method="post">
    <textarea rows="15" bind:value={content}></textarea>
    <input type="submit" value="답변등록" on:click="{post_comment}">
</form>