# git 설정(git config)

## 설정값 조회

### 모든 설정값 조회
```bash
git config --list
```

### 특정 설정값 조회
`--get` 옵션을 명시적으로 사용해서 조회할 수 있다.

```bash
git config --get user.name
```

또는 `--get` 옵션을 생략하고 `git config` 명령어를 사용해도 된다.

```bash
git config user.name
```

`--get` 옵션을 사용하는 것이 좀더 '속성값을 읽어 들인다'는 의도를 명시적으로 나타내는 면이 있다.

## 설정 변경/삭제
