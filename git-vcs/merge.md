# Merge 커밋 메시지 수정

이미 commit한 메시지들은 `-i`, `--interactive` 옵션으로 수정할 수있다.

이 옵션으로 커밋의 위치 변경과 squash 까지 할 수 있다.

하지만 merge commit (`merge branch 'master' into 'dev'` 등) 은 보이지 않아 커밋 메시지를 수정하기가 어렵다.

{% embed url="https://stackoverflow.com/questions/9930637/edit-a-merge-commit-with-git-rebase" %}

`git rebase -i --rebase-merges HEAD~` 처럼 `--rebase-merges` 옵션을 붙이면 된다.

(git v2.22 이전이라면 --preserve-merges 옵션)
