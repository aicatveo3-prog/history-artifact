# assets 폴더

이 폴더는 **큰 파일(예: 300MB)** 을 올리기 위한 공간입니다.
`.gitattributes` 설정에 의해 이 폴더에 넣는 파일은 자동으로 **Git LFS**로 저장되므로,
GitHub의 100MB push 제한에 걸리지 않습니다.

## 큰 파일을 올리는 방법 (내 컴퓨터에서)

> ⚠️ GitHub 웹사이트에서 드래그로 올리는 방식은 파일당 **25MB** 제한이 있어
> 300MB 파일은 **반드시 아래처럼 git 명령으로** 올려야 합니다.

```bash
# 1. Git LFS 설치 (최초 1회)
#    Mac:     brew install git-lfs
#    Windows: https://git-lfs.com 에서 설치
#    Ubuntu:  sudo apt install git-lfs

# 2. 계정에 LFS 활성화 (최초 1회)
git lfs install

# 3. 저장소 최신 상태 받기
git pull

# 4. 올릴 파일을 이 assets 폴더로 복사한 뒤 커밋
git add assets/올릴파일이름.zip
git commit -m "Add large file to assets"
git push
```

## 참고: 무료 계정 LFS 한도
- 저장소 저장 용량 **1GB**, 월 다운로드 대역폭 **1GB**
- 초과 시 월 $5 Data Pack(50GB)으로 확장 가능
- LFS 파일 하나당 최대 2GB
