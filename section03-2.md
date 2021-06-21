immer 도입하기
----

```node.js
import producer from 'immer';


//이전상태를 액션을 통해 다음상태로 만들어 내는 함수 reducer(불변성은 지키면서)
//기본모양
return produce(state, (draft)=>{

});

```
state는 그대로 두고 draft만 사용   
```node.js
// 이전 상태를 액션을 통해 다음 상태로 만들어내는 함수(불변성은 지키면서)
const reducer = (state = initialState, action) => produce(state, (draft) => {
  switch (action.type) {
    case LOAD_POSTS_REQUEST:
      draft.loadPostsLoading = true;
      draft.loadPostsDone = false;
      draft.loadPostsError = null;
      break;
    case ADD_COMMENT_SUCCESS: {
    //게시글 찾아서
    case ADD_COMMENT_SUCCESS: {
      const post = draft.mainPosts.find((v) => v.id === action.data.postId);
      post.Comments.unshift(dummyComment(action.data.content));
      draft.addCommentLoading = false;
      draft.addCommentDone = true;
      break;
      // const postIndex = state.mainPosts.findIndex((v) => v.id === action.data.postId);
      // const post = { ...state.mainPosts[postIndex] };
      // post.Comments = [dummyComment(action.data.content), ...post.Comments];
      // const mainPosts = [...state.mainPosts];
      // mainPosts[postIndex] = post;
      // return {
      //   ...state,
      //   mainPosts,
      //   addCommentLoading: false,
      //   addCommentDone: true,
      // };
    }
  }
});
```
해당 문법을 작성하다보면 draft에 문법에러가 나는데
.eslintrc
```node.js
 "rules": {
    ...
    "no-param-reassign": "off"
  }

```
해당 설정을 off 해준다.

