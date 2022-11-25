<script>
    import fastapi from "../lib/api"
    import Error from "../components/Error.svelte"
    import { link, push } from 'svelte-spa-router'
    import { is_login, username } from "../lib/store"
    import moment from 'moment/min/moment-with-locales'
    moment.locale('ko')

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

    function delete_post(_post_id) {
        if(window.confirm('정말로 삭제하시겠습니까?')) {
            let url = "/api/post/delete"
            let params = {
                post_id: _post_id
            }
            fastapi('delete', url, params, 
                (json) => {
                    push('/')
                },
                (err_json) => {
                    error = err_json
                }
            )
        }
    }

    function delete_comment(comment_id) {
        if(window.confirm('정말로 삭제하시겠습니까?')) {
            let url = "/api/comment/delete"
            let params = {
                comment_id: comment_id
            }
            fastapi('delete', url, params, 
                (json) => {
                    get_post()
                },
                (err_json) => {
                    error = err_json
                }
            )
        }
    }
</script>


<div class="container my-3">
    <!-- 게시글 -->
    <h2 class="border-bottom py-2">{post.subject}</h2>
    <div class="card my-3">
        <div class="card-body">
            <div class="card-text" style="white-space: pre-line;">{post.content}</div>
            <div class="d-flex justify-content-end">
                <div class="badge bg-light text-dark p-2 text-start">
                    <div class="mb-2">
                        <img src="/bone.png" alt="Icon" width="24" height="24"/>
                        { post.user ? post.user.username : ""}
                    </div>
                    <div>{moment(post.create_date).format("YYYY년 MM월 DD일 hh:mm a")}</div>
                </div>
            </div>
            <div class="my-3">
                {#if post.user && $username === post.user.username }
                <a use:link href="/post-modify/{post.id}" 
                    class="btn btn-sm btn-outline-secondary">수정</a>
                <button class="btn btn-sm btn-outline-secondary"
                    on:click={() => delete_post(post.id)}>삭제</button>
                {:else if $username === "master-bong"}
                <button class="btn btn-sm btn-outline-secondary"
                    on:click={() => delete_post(post.id)}>삭제</button>
                {/if}
            </div>
        </div>
    </div>

    <button class="btn btn-secondary" on:click="{() => {
        push('/')
    }}">목록으로</button>

    <!-- 댓글 목록 -->
    <h5 class="border-bottom my-3 py-2">{post.comments.length}개의 댓글이 있습니다.</h5>
    {#each post.comments as comment}
    <div class="card my-3">
        <div class="card-body">
            <div class="card-text" style="white-space: pre-line;">{comment.content}</div>
            <div class="d-flex justify-content-end">
                <div class="badge bg-light text-dark p-2 text-start">
                    <div class="mb-2">
                        <img src="/bone.png" alt="Icon" width="24" height="24"/>
                        { comment.user ? comment.user.username : ""}
                    </div>
                    <div>{moment(comment.create_date).format("YYYY년 MM월 DD일 hh:mm a")}</div>
                </div>
            </div>
            <div class="my-3">
                {#if comment.user && $username === comment.user.username }
                <button class="btn btn-sm btn-outline-secondary"
                    on:click={() => delete_comment(comment.id) }>삭제</button>
                {:else if $username === "master-bong"}
                <button class="btn btn-sm btn-outline-secondary"
                    on:click={() => delete_comment(comment.id) }>삭제</button>
                {/if}
            </div>
        </div>
    </div>
    {/each}
    <!-- 댓글 등록 -->
    <Error error={error} />
    <form method="post" class="my-3">
        <div class="mb-3">
            <textarea rows="10" bind:value={content} disabled={$is_login ? "" : "disabled"} class="form-control" />
        </div>
        <input type="submit" value="댓글등록" class="btn btn-primary {$is_login ? '' : 'disabled'}" on:click="{post_comment}" />
    </form>
</div>