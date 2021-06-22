immer 도입하기
----

```nodejs
import producer from 'immer';


//이전상태를 액션을 통해 다음상태로 만들어 내는 함수 reducer(불변성은 지키면서)
//기본모양
return produce(state, (draft)=>{

});

```
state는 그대로 두고 draft만 사용   
```nodejs
// 이전 상태를 액션을 통해 다음 상태로 만들어내는 함수(불변성은 지키면서)
const reducer = (state = initialState, action) => produce(state, (draft) => {
  switch (action.type) {
    case LOAD_POSTS_REQUEST:
      draft.loadPostsLoading = true;
      draft.loadPostsDone = false;
      draft.loadPostsError = null;
      break;
    case ADD_COMMENT_SUCCESS: {
    //메인 포스트중에 내가원하는 게시글 찾아서 그 포스트에다가 더미데이터를 넣어준다.
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

    case ADD_POST_TO_ME:
      draft.me.Posts.unshift({ id: action.data });
      break;
      // return {
      //   ...state,
      //   me: {
      //     ...state.me,
      //     Posts: [{ id: action.data }, ...state.me.Posts],
      //   },
      // };
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




faker로 실감나는 데이터
---

```node.js
import faker from 'faker'
```
더미데이터를 만들어줄때 사용한다.   
우리가 임의로 데이터를 만들어준다 하면 네이밍 하는것부터 어렵지만 faker를 사용하게 되면   
알아서 원하는 갯수만큼 만들어준다   
```node.js
export const generateDummyPost = (number) => Array(number).fill().map(() => ({
  id: shortId.generate(),
  User: {
    id: shortId.generate(),
    nickname: faker.name.findName(),
  },
  content: faker.lorem.paragraph(),
  Images: [{
    src: faker.image.image(),
  }],
  Comments: [{
    User: {
      id: shortId.generate(),
      nickname: faker.name.findName(),
    },
    content: faker.lorem.sentence(),
  }],
}));
```

인피니트스크롤링
---
hasMorePost = 더 불러오는 속성.   
loadPostsLoading = 로딩중

```node.js
function* loadPosts(action) {
  try {
    // const result = yield call(loadPostsAPI, action.data);
    yield delay(1000);
    yield put({
      type: LOAD_POSTS_SUCCESS,
      data: generateDummyPost(10),
    });
  } catch (err) {
    console.error(err);
    yield put({
      type: LOAD_POSTS_FAILURE,
      data: err.response.data,
    });
  }
}
```
스크롤이 내려갔을때 불러오도록 설정하는 법   
```node.js
  useEffect(() => {
    function onScroll() {
    if (window.scrollY + document.documentElement.clientHeight > document.documentElement.scrollHeight - 300) {
       if (hasMorePost && !loadPostsLoading) {
          dispatch({
            type: LOAD_POSTS_REQUEST,
            data: mainPosts[mainPosts.length - 1].id,
          });
        }
      }
    }
    window.addEventListener('scroll', onScroll);
    return () => {
      window.removeEventListener('scroll', onScroll);
    };
  }, [mainPosts, hasMorePost, loadPostsLoading]);

```

window.scrollY + + document.documentElement.clientHeight 을 합치면   
document.documentElement.scrollHeight 이 같았을때가 스크롤을 전부 내린 상태   

스크롤은 이벤트가 너무 많이 진행되기때문에 saga에서 한번 이벤트가 발생했을때 다른리퀘스트는 무시할수있도록 해당설정을 준다.
5초동안 다른 이벤트는 무시.

```
function* watchLoadPosts() {
  yield throttle(5000, LOAD_POSTS_REQUEST, loadPosts);
}
```
그리고 리퀘스트가 가고있을 경우는 loadPostsLoading 속성값이 true   
그래서 로딩이 아닐때만 저렇게 실행될수 있게 해준다.

팔로우 언팔로우 구현하기
---
FollowButton.js
```node.js
import React, { useCallback } from 'react';
import { Button } from 'antd';
import PropTypes from 'prop-types';
import { useSelector, useDispatch } from 'react-redux';
import { FOLLOW_REQUEST, UNFOLLOW_REQUEST } from '../reducers/user';

const FollowButton = ({ post }) => {
  const dispatch = useDispatch();
  //자신의 정보를 가져온다.
  const { me, followLoading, unfollowLoading } = useSelector((state) => state.user);
  //팔로잉 여부 ( 내 팔로잉 리스트에 있다면 팔로잉 한거)
  const isFollowing = me?.Followings.find((v) => v.id === post.User.id);
  const onClickButton = useCallback(() => {
  //팔로잉 여부에 따라서 보내는 액션을구분
    if (isFollowing) {
      dispatch({
        type: UNFOLLOW_REQUEST,
        data: post.User.id,
      });
    } else {
      dispatch({
        type: FOLLOW_REQUEST,
        data: post.User.id,
      });
    }
  }, [isFollowing]);

  return (
    <Button loading={followLoading || unfollowLoading} onClick={onClickButton}>
      {isFollowing ? '언팔로우' : '팔로우'}
    </Button>
  );
};

FollowButton.propTypes = {
  post: PropTypes.object.isRequired,
};

export default FollowButton;

```
reducers/user.js
```node.js
   case FOLLOW_FAILURE:
      draft.followLoading = false;
      draft.followError = action.error;
      break;
    case UNFOLLOW_REQUEST:
      draft.unfollowLoading = true;
      draft.unfollowError = null;
      draft.unfollowDone = false;
      break;
    case UNFOLLOW_SUCCESS:
      draft.unfollowLoading = false;
      draft.me.Followings = draft.me.Followings.filter((v) => v.id !== action.data);
      draft.unfollowDone = true;
      break;
    case UNFOLLOW_FAILURE:
      draft.unfollowLoading = false;
      draft.unfollowError = action.error;
      break;
```
sagas/user.js
```node.js
function* follow(action) {
  try {
    // const result = yield call(followAPI);
    yield delay(1000);
    yield put({
      type: FOLLOW_SUCCESS,
      data: action.data,
    });
  } catch (err) {
    console.error(err);
    yield put({
      type: FOLLOW_FAILURE,
      error: err.response.data,
    });
  }
}
```
