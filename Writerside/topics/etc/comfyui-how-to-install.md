# ComfyUI 설치 방법

## 1. Python 설치

'파이썬' 공식 사이트 배포판을 설치해도되지만, 'AI' 관련 각종 파이썬 라이브러리들이 지원하는 파이썬 버전이 다른 경우가 있다.
때문에 [Anaconda(아나콘다)](https://www.anaconda.com/) 같은 '파이썬 가상 환경' 구성이 가능한 배포판을 설치해서 상황에 따라 이용할
'파이썬 버전'을 바꾸어 작업하는 방식이 편리할 수 있다. 공식 배포본만을 통해서 파이썬 가상 환경 구성이 가능한 것으로 보이나 아나콘다를 사용하는
것이 더 편리하다.


## 2. Python 가상 환경 구성

'윈도OS'의 경우 아나콘다를 설치하면 'Anaconda Prompt'라는 프로그램이 설치된다. 아나콘다 관련 조작을 하는데 필요한 환경 정보등이 설정된
전용 '터미널' 앱이라고 보면 되겠다. 'macOS'의 경우는 기본으로 설치된 '터미널' 앱을 사용하면 된다.

터미널 앱에서 아나콘다가 제공하는 파이썬 가상 환경 구성이 가능한 `conda` 명령어를 사용할 수 있다. `conda` 명령어를 사용해서 'ComfyUI'에서
지원하는 '파이썬 가상 환경'을 구성할 수 있다.

```bash
conda create -n comfyui python=3.11
```

여기서 `-n`은 `--name` 옵션의 축약형이고, 'comfyui'는 설정할 파이썬 '가상 환경 이름'이다. `python=3.11`은 사용할 파이썬의 버전을
'3.11'로 지정한 것이다.

설정한 'comfyui'라는 이름의 파이썬 가상 환경을 활성화 시키려면 다음과 같이 입력한다.

```bash
conda activate comfyui
```

'comfyui'라는 이름의 파이썬 가상 환경을 활성화 시키면 터미널 앞에 `(comfyui)`라는 표시가 나타난다. 이 표시가 나타나면 'comfyui'라는
이름의 파이썬 가상 환경이 활성화 되었다는 것이다. 설정한 파이썬의 버전이 '3.11'인지 확인하려면 다음과 같이 입력한다.

```bash
python --version
```

'comfyui'라는 이름의 파이썬 가상 환경을 비활성화 시키고 기본적으로 설정되는 'base'라는 이름의 파이썬 가상 환경으로 돌아가려면 다음과 같이
입력한다.

```bash
conda deactivate
```

아나콘다를 통해서 설정한 파이썬 가상 환경 목록을 확인하려면 다음과 같이 입력한다.

```bash
conda env list
```

더이상 불필요한 파이썬 가상 환경을 삭제하려면 다음과 같이 입력한다.

```bash
conda remove -n comfyui --all
```

여기서 `--all`은 해당 파이썬 가상 환경에 설치된 모든 패키지를 삭제하라는 옵션이다.


## 3. Windows OS에서 ComfyUI 설치

'윈도OS'의 경우는 ComfyUI GitHub 저장소에서 '포터블'하게 설치할 수 있도록 '.7z' 파일로 압축된 파일을 제공하고 있다. 압축해제 후 곧바로
실행이 가능하다. '파이썬 실행 파일'과 'ComfyUI' 저장소 자체를 포함하고 있다.

압축해제 후 최상위 폴더에 있는 'README_VERY_IMPORTANT.txt' 파일을 열어서 안내에 따라 실행하면된다. 직접 읽어 보는게 좋겠다. 여기서는
필요한 내용만 간략하게 설명한다.

'터미널' 앱을 실행하고 압축해제한 최상위 폴더 위치에서 포터블 배포판에 포함된 'ComfyUI' 저장소등을 최신으로 업데이트 하기 위해서 다음과 같이
커맨드를 실행한다.

```bash
cd update
update_comfyui.bat
```

'update' 폴더로 이동하는 이유는 'update_comfyui.bat' 스크립트내에서 현재 폴더 위치를 'update' 폴더로 가정하여 '상대경로'로 작성되어
있기 때문이다. 폴더를 이동하지 않고 'update_comfyui.bat' 스크립트를 실행하면 관련 파일을 찾을 수 없어 오류가 발생한다. 가능하면 최신의 상태로
유지하는 것이 좋을 것이기 때문에 필요하다면 실행전 업데이트를 해주는 것이 맞겠다.

업데이트 후 압축해제 폴더 최상위 위치에서 다음 명령어를 입력해서 'ComfyUI'를 실행할 수 있다.

```bash
run_nvidia_gpu.bat
```

'NVIDIA GPU'를 사용하는 경우에는 위 명령어를 입력해서 실행하면 된다. 'NVIDIA GPU'를 사용하지 않는 경우에는 다음 명령어를 입력해서 실행하면
된다.

```bash
run_cpu.bat
```

## 4. ComfyUI 수동 설치

'윈도OS'가 아니거나, '윈도OS'일 경우에도 직접 '수동 설치'를 하고 싶다면 다음과 같이 한다.

우선 'ComfyUI' GitHub 저장소로부터 '저장소 복제'를 한다. 현재 시점에서 패키지 형태의 설치가 지원되지 않기 때문에 '저장소 복제'를 통해서
'ComfyUI'를 설치해야 한다. 적당한 작업 폴더를 생성 후 다음과 같이 `git clone` 명령어를 입력한다.

```bash
git clone https://github.com/comfyanonymous/ComfyUI.git
```

## 5. '모델' 파일 다운로드 및 설치

1. Stable Diffusion 1.5 : <https://huggingface.co/runwayml/stable-diffusion-v1-5/blob/main/v1-5-pruned-emaonly.ckpt>
2. Stable Diffusion XL 1.0
    - base : <https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0/blob/main/sd_xl_base_1.0.safetensors>
    - refiner : <https://huggingface.co/stabilityai/stable-diffusion-xl-refiner-1.0/blob/main/sd_xl_refiner_1.0.safetensors>
