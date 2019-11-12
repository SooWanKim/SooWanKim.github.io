---
layout: post
title: Checking meta file with using git hook
categories: [Git]
---

##  precommit 사용하기 Unity Meta에서 커밋 체크

유니티3D 프로젝트를 git으로 버전관리할 때 불편한 점 중에 하나는 빈 디렉토리의 meta파일

유니티 프로젝트 내에 빈 디렉토리를 만들면 유니티는 그 디렉토리에 대한 meta파일을 생성합니다.

하지만 git은 빈 디렉토리를 무시하기 때문에 이 상태에서 커밋을 하게 되면 meta파일만 git에 추가됩니다.

다른 팀원이 이 코드를 pull 받게 되면 디렉토리 없이 meta파일만 내려받게 되고 유니티 에디터는 불필요한 meta파일 발견 경고를 내면서 meta파일을 삭제합니다.

A팀원은 meta파일을 계속 자동 추가해서 커밋하고 B팀원은 meta파일을 계속 자동 삭제해서 커밋하는 상황이 발생하기도 합니다.

이 현상을 방지하기 위해 사용할 수 있는 방법 중 하나가 git의 hook을 활용하는 방법입니다.

hook은 어떤 이벤트가 생겼을 때 자동으로 특정 스크립트를 실행하도록 할 수 있도록 하는 기능입니다.


여기서는 불필요한 meta 파일이 커밋되지 않도록 pre-commit hook을 사용해서 막아보려고 합니다.


`pre-commit` 스크립트 파일을 적용하려는 프로젝트의 .git 폴더 아래에 hooks폴더를 만들어 그 아래에 두면 즉시 적용 됩니다.

스크립트 내용은 다음과 같습니다.

pre-commit 파일 내용
```
#!/bin/bash
#
# An example hook script to verify what is about to be committed.
# Called by "git commit" with no arguments.  The hook should
# exit with non-zero status after issuing an appropriate message if
# it wants to stop the commit.
#
# To enable this hook, rename this file to "pre-commit".

if git rev-parse --verify HEAD >/dev/null 2>&1
then
    against=HEAD
else
    # Initial commit: diff against an empty tree object
    against=4b825dc642cb6eb9a060e54bf8d69288fbee4904
fi

# Redirect output to stderr.
exec 1>&2

git diff --cached --name-only --diff-filter=A -z $against -- Assets | while read -d $'\0' f; do
    ext="${f##*.}"
    base="${f%.*}"

    if [ "$ext" = "meta" ]; then
        if [ $(git ls-files --cached -- "$base" | wc -l) = 0 ]; then
            cat <<EOF
오류: 불필요한 메타 파일이 포함되어 있습니다.
메타 파일 \`$f' 이 추가되었으나, \`$base' 가 git index에 없습니다.
\`$base'를 git index에 함께 추가해주시거나, \`$base'가 빈 디렉토리라면 \`$f'와 함께 제거해주세요.

EOF
            exit 1
        fi
    else
        p="$f"
        while [ "$p" != "Assets" ]; do
            if [ $(git ls-files --cached -- "$p.meta" | wc -l) = 0 ]; then
                cat <<EOF
오류: 메타 파일이 누락되었습니다.
어셋 \`$f' 이 추가되었으나,, \`$p.meta' 메타 파일이 git index에 없습니다.
\`$p.meta'도 함께 추가해주세요.
EOF
                exit 1
            fi
            p="${p%/*}"
        done
    fi
done

ret="$?"
if [ "$ret" != 0 ]; then
    exit "$ret"
fi

git diff --cached --name-only --diff-filter=D -z $against -- Assets | while read -d $'\0' f; do
    ext="${f##*.}"
    base="${f%.*}"

    if [ "$ext" = "meta" ]; then
        if [ $(git ls-files --cached -- "$base" | wc -l) != 0 ]; then
            cat <<EOF
오류: 잘못된 메타 파일 삭제.
메타 파일 \`$f' 이 삭제되었으나, \`$base'이 git index에 남아 있습니다.
\`$base'도 함께 삭제하시거나 \`$f'제거를 폐기해주세요.
EOF
            exit 1
        fi
    else
        p="$f"
        while [ "$p" != "Assets" ]; do
            if [ $(git ls-files --cached -- "$p" | wc -l) = 0 ] && [ $(git ls-files --cached -- "$p.meta" | wc -l) != 0 ]; then
                cat <<EOF
오류: 메타 파일 삭제 누락.
어셋 \`$f'이 삭제되었으나, 메타 파일 \`$p.meta'이 git index에 남아 있습니다.
\`$p.meta'도 함께 삭제하시거나 \`$f'제거를 폐기해주세요.
EOF
                exit 1
            fi
            p="${p%/*}"
        done
    fi
done

ret="$?"
if [ "$ret" != 0 ]; then
    exit "$ret"
fi
```
***
## 스크립트 역할

1. meta 파일을 추가하려고 하는데 그에 상응하는 어셋이 추가되지 않았을 경우 오류 처리
2. 어셋을 추가하려고 하는데 그에 상응하는 meta파일이 추가되지 않았을 경우 오류 처리
3. meta 파일을 삭제하려고 하는데 그에 상응하는 어셋이 삭제되지 않았을 경우 오류 처리
4. 어셋을 삭제하려고 하는데 그에 상응하는 meta파일이 삭제되지 않았을 경우 오류 처리