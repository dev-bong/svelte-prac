<script>
    import fastapi from "../lib/api"
    import Error from "../components/Error.svelte"
    import { push } from 'svelte-spa-router'

    export let params = {}
    let post_id = params.post_id
    let post = {comments:[]}
    let content = ""
    let error = {detail:[]}

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
                error = {detail:[]}
                get_post()
            },
            (err_json) => {
                error = err_json
            }
        )
    }
</script>


<div class="container my-3">
    <!-- 질문 -->
    <h2 class="border-bottom py-2">{post.subject}</h2>
    <div class="card my-3">
        <div class="card-body">
            <div class="card-text" style="white-space: pre-line;">{post.content}</div>
            <div class="d-flex justify-content-end">
                <div class="badge bg-light text-dark p-2">
                    {post.create_date}
                </div>
            </div>
        </div>
    </div>
    
    <button class="btn btn-secondary" on:click="{() => {
        push('/')
    }}">목록으로</button>

    <!-- 답변 목록 -->
    <h5 class="border-bottom my-3 py-2">{post.comments.length}개의 댓글이 있습니다.</h5>
    {#each post.comments as comment}
    <div class="card my-3">
        <div class="card-body">
            <div class="card-text" style="white-space: pre-line;">{comment.content}</div>
            <div class="d-flex justify-content-end">
                <div class="badge bg-light text-dark p-2">
                    {comment.create_date}
                </div>
            </div>
        </div>
    </div>
    {/each}
    <!-- 답변 등록 -->
    <Error error={error} />
    <form method="post" class="my-3">
        <div class="mb-3">
            <textarea rows="10" bind:value={content} class="form-control" />
        </div>
        <input type="submit" value="댓글등록" class="btn btn-primary" on:click="{post_comment}" />
    </form>
</div>