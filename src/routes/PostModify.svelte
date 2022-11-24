<script>
    import { push } from 'svelte-spa-router'
    import fastapi from "../lib/api"
    import Error from "../components/Error.svelte"

    export let params = {}
    const post_id = params.post_id

    let error = {detail:[]}
    let subject = ''
    let content = ''

    fastapi("get", "/api/post/detail/" + post_id, {}, (json) => {
        subject = json.subject
        content = json.content
    })

    function update_post(event) {
        event.preventDefault()
        let url = "/api/post/update"
        let params = {
            post_id: post_id,
            subject: subject,
            content: content,
        }
        fastapi('put', url, params, 
            (json) => {
                push('/detail/'+post_id)
            },
            (json_error) => {
                error = json_error
            }
        )
    }
</script>

<div class="container">
    <h5 class="my-3 border-bottom pb-2">게시글 수정</h5>
    <Error error={error} />
    <form method="post" class="my-3">
        <div class="mb-3">
            <label for="subject">제목</label>
            <input type="text" class="form-control" bind:value="{subject}">
        </div>
        <div class="mb-3">
            <label for="content">내용</label>
            <textarea class="form-control" rows="10" bind:value="{content}"></textarea>
        </div>
        <button class="btn btn-primary" on:click="{update_post}">수정하기</button>
    </form>
</div>