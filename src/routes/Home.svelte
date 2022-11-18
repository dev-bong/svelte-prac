<script>
    import fastapi from "../lib/api"
    import { link } from 'svelte-spa-router'

    let post_list = []
  
    function get_post_list() {
        fastapi('get', '/api/post/list', {}, (json) => {
            post_list = json
        })
    }
  
    get_post_list()
  </script>


  <div class="container my-3">
    <table class="table">
        <thead>
        <tr class="table-dark">
            <th>번호</th>
            <th>제목</th>
            <th>작성일시</th>
        </tr>
        </thead>
        <tbody>
        {#each post_list as post, i}
        <tr>
            <td>{i+1}</td>
            <td>
                <a use:link href="/detail/{post.id}">{post.subject}</a>
            </td>
            <td>{post.create_date}</td>
        </tr>
        {/each}
        </tbody>
    </table>
    <a use:link href="/post-create" class="btn btn-primary">게시글 등록하기</a>
</div>