---
draft: false
date: 2024-05-15
authors:
  - wath1457
categories:
  - CI/CD
  - troubleshooting
comments: true
---

# mkdocs-material troubleshooting
<br>

mkdocs-material with github pages

수많은 디버깅 끝에 배포 완료..
gitlab Pages는 회사에서 사용중이고 github actions는 거의 사용을 안해봐서 공부도 할겸 github actions를 이용한 pages를 선택했다.


<br><br>
이런식으로 블로그를 운영하면 얻을 수 있는 장점은
<br>
<!-- more -->
1. 내 입맛대로 블로그 커스터마이징 가능
2. 기본적인 git flow와 workflow(CI) 경험 가능 (요건 github actions를 이용하는 경우)
    - pages를 branch 배포가 아니라, github actions를 이용하면 좀 더 어렵겠지만 많은 공부가 될 것 같다.
3. 정적 사이트를 렌더링 하는 과정에서 다양한 지식 습득 가능..
    - mkdocs의 경우에는 마크다운 문법... 및 도커(도커 이미지로도 mkdocs-material을 제공해준다)
4. (중요!) github 잔디 심기
    - 아 이건 너무 보여주기식인데ㅋㅋ 잔디심는거 좋아하시는 분들은 블로그 포스팅만으로 깃헙에 잔디 심기가 가능하다~~ (~~개꿀이다~~)

<hr>
<br><br>
### mkdocs & github pages
우선 간단하게 mkdocs와 github pages에 대해 설명하자면..
<br><br>
gitlab pages는 간단하게 말하면 정적 페이지를 편하게 배포해주는 기능이라 생각하면 될 것 같다.
<br><br>
mkdocs는 Markdown파일을 이용하여 정적 사이트를 렌더링해주는 편리한 문서 작성 도구이고, 나는 mkdocs에 material UI 테마를 적용하였다.
<br><br><br>
버그때문에 생각보다 많이 늦어졌다..
<br><br>
github actions에서는 생각보단 문제가 없었는데, blog plugin관련한 버그때문에.. 늦어져서 관련 트러블슈팅 내용으로 첫 포스팅을 하려고 한다.
<br><br><br>

<hr>

### blog plugin의 포스트가 표시되지 않는 문제

이것때문에 꽤 많은 시간을 보냈다.
<br><br>
처음에는 github action에서 무엇인가 잘못된줄 알고 애꿎은 workflow(ci파일)을 계속 수정해봤는데 동작하지 않았다.
<br><br>
이 문제가 웃긴게 local docker환경에서 해당 명령어로 만들어진 정적 사이트에선 에러없이 잘 동작해서 원인을 찾기 힘들었다.
```bash
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
```
<br><br>

내 경우에는 테스트 포스트를 작성할때, 메타데이터의 `draft` 때문에 발생한 문제였다.
```markdown
---
draft: true
date: 2024-05-12
authors:
  - wath1457
categories:
  - Kubernetes
  - DevOps
---
```
<br>
포스트에 설정하는 메타데이터에 `draft: true`로 되어있었다.
<br><br>
이것 자체는 문제가 없는데,
<br><br>
문제는 `mkdocs.yml`파일에서 따로 설정을 하지 않는 이상 `draft: false`로 기본으로 `draft`가 비활성화가 되어있는 것이다.
<br><br>
메타데이터에 draft를 제거하거나, mkdocs.yml의 `draft: true`를 해줘야 제대로 동작을 하게 된다.
<br><br>
뇌피셜이긴한데
```bash
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
```
<br>
~~이렇게 하면 `.md`파일을 렌더링하는 과정 없이, 배포를 진행하는 것이고,
저렇게 설정이 충돌이 발생하게 되는 순간, 정적 사이트로 build를 진행하면서 문제가 발생하는 것 같다.~~

<br><br><br>
### author의 avatar(프로필 사진)이 게시글에서 보이지 않는 문제

왜 그런건지 모르겠는데 이렇게 이미지 경로 맨 앞에  '/'를 붙이지 않으면 게시글 미리보기에선 프로필사진이 보이는데 게시글 상세페이지에서 이미지가 안보인다.
```yml
authors:
  ...
    ...
    avatar: images/avatar.jpg
```
![ss](/images/mkdocs-troubleshooting/2.PNG)

<hr>

'/'를 붙여주면 정상적으로 프로필 사진이 보인다.
```yml
authors:
  ...
    ...
    avatar: /images/avatar.jpg
```
![ss](/images/mkdocs-troubleshooting/1.PNG)

<hr/>

일단 여기까지가 해결하기 어려웠던 문제였고.. 나머지는 [mkdocs-material 공식문서](https://squidfunk.github.io/mkdocs-material/)를 보면 된다!
<br><br>
아직은 블로그가 많이 허접한데, 나중에 좀 꾸며줄 예정이다..ㅎㅎ(언제?)
<br><br>
다음은 일단 블로그에 댓글 플러그인 적용해야겠다..