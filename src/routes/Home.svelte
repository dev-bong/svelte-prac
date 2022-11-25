<script>
    import fastapi from "../lib/api"
    import { link } from 'svelte-spa-router'
    import { page, is_login } from "../lib/store"
    import moment from 'moment/min/moment-with-locales'
    moment.locale('ko')

    let post_list = []
    let size = 10
    let total = 0
    $: total_page = Math.ceil(total/size)
  
    function get_post_list(_page) {
        let params = {
            page: _page,
            size: size
        }
        fastapi('get', '/api/post/list', params, (json) => {
            post_list = json.post_list
            $page = _page
            total = json.total
        })
    }
  
    $: get_post_list($page)
  </script>


  <div class="container my-3">
    <table class="table">
        <thead>
        <tr class="text-center table-dark">
            <th>번호</th>
            <th style="width:50%">제목</th>
            <th>글쓴이</th>
            <th>작성일시</th>
        </tr>
        </thead>
        <tbody>
        {#each post_list as post, i}
        <tr class="text-center">
            <td>{ total - ($page * size) - i }</td>
            <td class="text-start">
                <a use:link href="/detail/{post.id}">{post.subject}</a>
                {#if post.comments.length > 0 }
                <span class="text-danger small mx-2">{post.comments.length}</span>
                {/if}
            </td>
            <td>
                {#if post.user && post.user.icon }
                    <img src="/{post.user.icon}.png" alt="Icon" width="36" height="36"/>
                {:else}
                    <img src="/bone.png" alt="Icon" width="36" height="36"/>
                {/if}
                { post.user ? post.user.username : "" }
            </td>
            <td>{moment(post.create_date).format("YYYY년 MM월 DD일 hh:mm a")}</td>
        </tr>
        {/each}
        </tbody>
    </table>
    <!-- 페이징처리 시작 -->
    <ul class="pagination justify-content-center">
        <!-- 이전페이지 -->
        <li class="page-item {$page <= 0 && 'disabled'}">
            <button class="page-link" on:click="{() => get_post_list($page-1)}">이전</button>
        </li>
        <!-- 페이지번호 -->
        {#each Array(total_page) as _, loop_page}
        {#if loop_page >= $page-5 && loop_page <= $page+5}
        <li class="page-item {loop_page === $page && 'active'}">
            <button on:click="{() => get_post_list(loop_page)}" class="page-link">{loop_page+1}</button>
        </li>
        {/if}
        {/each}
        <!-- 다음페이지 -->
        <li class="page-item {$page >= total_page-1 && 'disabled'}">
            <button class="page-link" on:click="{() => get_post_list($page+1)}">다음</button>
        </li>
    </ul>
    <!-- 페이징처리 끝 -->
    <a use:link href="/post-create" class="btn btn-primary {$is_login ? '' : 'disabled'}">게시글 등록하기</a>
</div>