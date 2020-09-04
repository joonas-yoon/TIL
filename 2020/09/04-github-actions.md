# 2020-09-04

## Github Actions

### HEAD~n

Github Actions에서 `actions/checkout@v2`는 최근 1개의 히스토리만 가져온다. 그래서 `HEAD~1` 등을 하면 해당 리비전(revision)을 찾을 수 없다고 나온다.

이를 해결하기 위해서는 `fetch-depth` 옵션을 추가해야한다. [#](https://github.com/actions/checkout#checkout-head)

```
- uses: actions/checkout@v2
  with:
    fetch-depth: 2
- run: git checkout HEAD^
```

### Error with exit code 1.

분명 문제 없이 프로세스를 끝냈지만, 아무 로그도 없이 빌드에 실패했다고 나오는 경우가 있다.

yml 파일과 그 결과는 이랬다.

```
- run: MSG=`git diff HEAD HEAD~1 --name-only | egrep [0-9]+\/`
```

```
##[error]Process completed with exit code 1.
```

이것과 관련해서는 아래 링크로 해결할 수 있다.

https://github.com/actions/virtual-environments/issues/1279#issuecomment-663462004

3가지 케이스로 실험했는데 결과는 다음과 같다.

1. `./test.sh`로 실행 (성공)
2. `source ./test.sh`로 실행 **(실패)**
3. `source ./test.sh || true`로 실행 **(성공)**

이 사례로 보아, 쉘에서 스크립트를 직접 실행하면 exit code가 0이 아닌 것으로 보인다. (`source`로만 실행 시 실패인 것을 참고)
