# 가상 환경

## Anaconda(아나콘다)

'파이썬' 가상 환경을 만들어주는 기능을 포함한 '파이썬 배포판'중 하나이다. `conda` 명령어를 통해 '특정 버전의 파이썬' 가상 환경을 설정하고 관리할 수 있다.
생성된 가상 환경에 '특정 버전의 패키지'를 설치할 수 있다. 여러 가상 환경간에 자유로운 전환이 가능하다.

'아나콘다'를 설치한 것인지 확인하려면 `conda --version` 명령어를 입력한다.

아나콘다로 생성한 가상 환경 목록을 확인하려면 `conda env list` 또는 `conda info --envs` 명령어를 입력한다.

별도의 추가적인 가상 환경을 생성한 적이 없다면 `base`라는 이름의 가상 환경만 존재한다. macOS의 경우 '아나콘다'가 설치된 직후에 '터미널' 앱을 실행하면
`base`라는 이름의 가상 환경이 자동으로 활성화된다. 터미널의 커맨드라인 앞부분에 `(base)`라는 표시가 나타난다.

'아나콘다'로 가상 환경을 생성하려면 `conda create --name <가상 환경 이름> <패키지 이름>` 명령어를 입력한다.
`conda create --name myenv python=3.11` 명령어를 입력하면 'python 3.11' 버전을 사용하는 'myenv'라는 이름의 가상 환경이 생성된다.
단축형 명령어로 `conda create -n myenv python=3.11` 명령어를 입력할 수도 있다.

'아나콘다'로 생성한 가상 환경을 활성화하려면 `conda activate <가상 환경 이름>` 명령어를 입력한다. `conda activate myenv` 명령어를 입력하면
'myenv'라는 이름의 가상 환경이 활성화된다. 참고로 'macOS'에서 '터미널' 앱을 새로 실행하면 마지막으로 활성화한 가상 환경이 아니라 'base' 가상 환경이
자동으로 활성화된다.

'아나콘다'로 생성한 가상 환경을 비활성화하려면 `conda deactivate` 명령어를 입력한다. `conda deactivate` 명령어를 입력하면 현재의 가상 환경이
비활성화되고 `base` 가상 환경이 활성화된다.

'아나콘다'로 생성한 가상 환경을 삭제하려면 `conda remove --name <가상 환경 이름> --all` 명령어를 입력한다.
`conda remove --name myenv --all` 명령어를 입력하면 'myenv'라는 이름의 가상 환경이 삭제된다.
단축형 명령어로 `conda remove -n myenv --all` 명령어를 입력할 수도 있다. 여기서 `--all` 옵션은 '가상 환경'에 설치된 '패키지'까지 모두 삭제한다는
의미이다.

각 가상 환경에 설치된 패키지 목록을 확인하려면 `conda list` 명령어를 입력한다.

'comfyui'라는 이름의 '3.11' 버전의 파이썬 가상 환경을 만들고 활성화 시키기 위해서는 다음과 같이 명령어를 입력하면 된다.

```bash
conda create -n comfyui python=3.11
conda activate comfyui
```

파이썬의 공식 '패키지 매니저'인 `pip`을 새로 생성한 가상 환경에서 사용할 수 있지만, '아나콘다' 배포판을 설치한 것이니 '패키지 매니저'를 겸한
`conda` 명령어를 사용하는 것이 더욱 편리하다. 생성한 가상 환경으로 전환 후에 다음과 같이 `conda` 명령어를 사용해 해당 가상 환경에서 사용할 패키지를
설치할 수 있다.

```bash
conda install <패키지 이름>
```

예를 들어서 'myjupyter' 가상 환경을 설정하고 그 가상 환경에 'JupyterLab'을 설치하고 실행하려면 다음과 같이하면 된다.

```bash
conda create -n myjupyter python=3.11
conda activate myjupyter
conda install jupyterlab
jupyter lab
```
