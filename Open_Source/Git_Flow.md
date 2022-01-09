### Git Pull Request [PR]

GitHub을 통한 협업의 필수인 Pull Request를 통한 contribution을 알아보자.

### Git Flow

<img src="./readmeImg/git_flow.png" alt="git_flow" style="zoom:60%;" /> 

Git Flow는, 개발 과정충 Merge (머지 - 병합) 과정에서 충돌이 일어날 수 있는데, 이러한 충돌을 최대한 피하고자 하는 전략입니다.

두 가지 branch:

- master: 기준이 되는 브랜치로 실제 사용자가 보게 되는 서비스가 배포되어있는 브랜치입니다.
- develop : 개발이 활발히 이뤄지는 브랜치로 기능을 구현한 브랜치는 항상 여기에 머지됩니다.

Git Flow에서 가장 중요한 점은, **브린치의 역할에 따라 무조건 지정된 브랜치에만 머지해야한다는 점**입니다.

기능을 구현한 브랜치는 무조건 develop 브랜치에 머지해야 하고, develop 브랜치에 있는 기능들을 출시(배포)할 때에만 master 브랜치에 머지해야 한다는 것입니다. Git Flow를 사용하면서 개발할 때엔 다음과 같은 단계를 거쳐 머지합니다.

### Pull Reuqest, PR

PR은 협업을 할 때 가장 중요한 기능이다. 전체 code project와 변경사항 [commit 내역]을 merge 하기 전에, 확인을 받는 절차이다. 보통 PR review (= code review)를 받는다.

팀원에게 확인을 받을 수도 있고, 자동화된 절차에 의해 확인을 받을수 있습니다. 전자는 PR review를 통해, 후자는 Action에 의해 확인을 받을 수 있습니다. 위 화면을 보면 이슈 세부화면과 별 다를게 없고, 위에서 설명한 것과 같게 작동됩니다. 다만 우측에 Reviewers라는 탭이 추가된 것을 볼 수 있습니다. Reviewers를 통해 이 브랜치, 이 PR을 리뷰할 팀원을 지정할 수 있습니다. 리뷰어로 지정이 되면, 상단과 같이 Add your review 버튼을 눌러 리뷰를 시작할 수 있습니다.



1. fork 를 통해 기여하고자 하는 project를 나의 github 저장소로 복사 (fork) 한다.
2. 내 컴퓨터에 저장소를 Clone 받아보자.
   - $ git clone https://github.com/[repository]
   - 또는, IntelliJ등의 IDE를 통해 github repository를 clone 하자.
3. Branch 를 자신만의 branch로 변경하여 commit 내역을 올리자.
4. Compare & Pull Request를 올리자.
   - 내용을 잘 입력하자.
5. PR
   - Comment : 승인과 무관하게 일반적인 커멘트를 할 때 선택합니다.
   - Approve : Comment와 다르게 리뷰어가 승인을 하는 것으로, 머지해도 괜찮다는 의견을 보내는 것입니다.
   - Request changes : 말 그대로 변경을 요청하는 것으로, 승인을 거부하는 것을 말합니다. 머지하면 안된다는 의견을 보내는 것이기도 하죠.
6. 리뷰나 액션을 통해 충분히 확인되었다면 브랜치를 develop에 머지합니다.
7. 이후 충분히 기능이 구현되어 develop에 머지된다면 develop을 master에 머지해 배포합니다.