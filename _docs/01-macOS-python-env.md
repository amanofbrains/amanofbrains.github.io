---
title:  "macOS Monterey 12.3.1 Python3 환경 설정"
excerpt: "Apple m1 Chipset"
---

데이터 사이언스라는걸 해보기 위해 파이썬을 선택했다. 물론 구글링을 하다보니 파이썬용 예제도 많았고, 특히 유대표, 조대표 공저의 "[비트코인 자동 매매](https://wikidocs.net/book/1665)"라는 책을 어떻게 어떻게 입수하게 되었고, 데이터 전처리에서부터 통계정 분석 방법과 머신러닝 기법에 대한 라이브러리를 쉽게 사용할 수 있다고 판단되어 파이썬을 선택하게 되었다.<br>
macOS에도 익숙하지 않고, 가끔 마우스 휠도 헤갈리는 처지라 최대한 처음부터 정리해 보려고 한다.<br>

# 1. homebrew<br>
macOS의 패키지 관리자 homebrew를 설치하기<br>
[Homebrew 홈 페이지](https://brew.sh/index_ko)<br>

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"<br>

위 명령어로 homebrew를 설치하게 되면 /opt/homebrew 폴더 아래 설치가 된다. 이는 arm64 arch 계열의 m1 chip일 경우 해당 폴더에 설치가 되고, 이후 brew 명령어를 통해 패키지를 설치하게 되면 /opt/homebrew/Cellar 폴더 아래에 설치가 된다. 또 설치된 패키지들은 brew list 명령을 통해서도 확인이 가능하다.<br>

# 2. pyenv<br>
이제 파이썬을 위한 pyenv를 brew를 통해 설치해 본다.<br>
pyenv는 여러개의 파이썬 버전을 관리해 줄 수 있는 패키지로 여러 파이썬 버전을 설치하고 선택 관리할 수 있도록 해준다. virtualenv까지 설치하기도 하지만, 그렇게까지 사용할것 같지 않아 생략한다.<br>

brew install pyenv<br>

brew list<br>
=> pyenv 목록에서 확인<br>

# 3. python<br>
최신 버전의 파이썬의 경우 라이블러리 호환성 이슈가 있다는 언급이 있어 금일 기준 3.10.4이 있었지만, 3.9.12버전을 선택했다.<br>

pyenv install -l<br>

위 명령어로 pyenv로 설치 가능한 파이썬 버전 목록을 확인 할 수 있다.<br>
설치가 완료 되면 파이썬 디폴트 버전을 설치한 버전을 변경한다.<br>

pyenv versions<br>
pyenv global 3.9.12<br>
pyenv versions<br>

zsh을 기본으로 사용하기 때문에 ~/.zshrc 파일에 아래 스크립트를 추가한다.<br>

if command -v pyenv 1>/dev/null 2>&1; then<br>
  eval "$(pyenv init -)"<br>
fi<br>

이후 zsh을 재실행하거나 source ~/.zshrc 명령으로 쉘 설정을 리로드 시킨다.<br>

which python<br>
 => /User/user-name/.pyenv/shims/python<br>
 
python -V<br>
 -> Python 3.9.12<br>

# 4. pip<br>
데이터 분석을 위해 파이썬 패키지 관리자인 pip으로 필요한 파이썬 패키지를 설치한다.<br>
데이터 분석을 위한 기본 패키지 : 다른 패키지와의 의존성으로 자동 설치도 됨.<br>

pip install pandas<br>
pip install beautifulsoup4<br>
pip install requests<br>
pip install numpy<br>
pip install scipy<br>

바이넨스와 업비트 데이터를 가져오기 위한 패키지<br>
pip install python-binance<br>
 => [python-binance](https://python-binance.readthedocs.io/en/latest/)<br>
 
pip install binance-connector<br>
 => [binance-connector](https://github.com/binance/binance-connector-python)<br>
 
pip install pyupbit<br>
 => [pyupbit](https://github.com/sharebook-kr/pyupbit)<br>

# 5. 차트 지표 및 백테스팅을 위한 라이브러리<br>
ta-lib는 BSD 라이센스로 150개 이상의 차트 분석 지표(MACD, RSI, Stochastic, Bollinger Bands etc)를 계산해주는 api를 제공해 주고 있다. 이 파이썬 라이브러리는 추가로 Cython 기반의 [TA-LIB](https://ta-lib.org/)를 wrapping하고 있는 패키지로 먼저 brew를 이용하여 해당 패키지를 설치한다.<br>

brew install ta-lib<br>
pip install ta-lib<br>

패키지를 모두 설치한 후 동작되는 것을 분명 확인했는데 x86_64 lib이 없다며 계속 오류가 발생되었다. 분명 arm64용 whl가 설치되었다고 로그를 확인했지만, file 명령어로 해당 라이브러리를 확인해보니 x86_64였고,<br>

pip install —no-cache-dir ta-lib <br>
 
로 재설치하여 동작은 확인하였다. 원인은 정확히 모르겠다.<br>

추가로 여러 백테스트 툴 중 [시스템 트레이닝을 위한 데이터 사이언스](https://wikidocs.net/60659)에서 소개된 zipline을 설치해보자. zipline의 경우 ta-lib을 사용하기 때문에 앞 ta-lib만 문제 없이 설치될것 같았지만 hd5 라이브러리 오류가 발생되었다. 다음과 같이 설치해 보자.<br>

brew install hdf5<br>
pip install h5py<br>
pip install zipline-reloaded<br>

일단 데이터 분석을 위한 필요한 기본 환경은 구축이된 듯하다. 차츰차츰 필요한 스터디를 해가면서 업데이트 해보려고 한다.<br>

