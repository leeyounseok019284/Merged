# 선택 감정과 이모티콘
## 제작의도
>저희가 어떤 생각으로 이 프로젝트를 진행하였는지, 그리고 어떤 프로그램이 목표인지를 설명하기 위해 작성했습니다.
>코드 완성 전까지는 계획이 바뀔 때마다 수정된 부분을 확인할 수 있는 곳으로 사용하기 위해 필요한 구간입니다.
----
TtS가 어색하게 느껴지는 이유 중 가장 큰 것은, 감정을 표현할 수 없는 목소리라는 점이라고 생각합니다. 하지만 이런 단점을 보완할 감정 추가라는 기능이 있는 프로그램이 있었기에, 그 프로그램의 특징을 활용하여 더 매력적인 프로그램으로 만들 수 있었으면 좋겠다고 생각했습니다.
때문에 emotivoice의 코드와 tweetvec2의 코드를 적절히 활용해서, 감정이 담긴 목소리를 생성할 때 함께 사용할 적절한 이모티콘을 추천해주는 프로그램을 만들 계획입니다.

## 프로그램 실행
>프로그램을 실행했을 때의 모습을 간단하게 보여주기 위해 작성했습니다.
>어떤 프로그램인지를 명시적으로 보여주기 위해 필요한 구간입니다.
---
![이미지 설명](영상대체예정.jpg)
영상, 스크린샷을 올릴 예정입니다.

## Emotivoice
>메인이 될 프로그램인 Emotivoice가 어떤 역할을 하는 것인지를 설명하기 위해 작성했습니다.
>코드 수정 전에, 각 파일들이 어떤 역할을 하는지를 쉽게 알아보기 위해 간단한 설명을 첨부하는 역할을 합니다.
>함수를 다 설명하기엔 길이 문제가 있으므로 변경할 가능성이 큽니다.
---
[emotivoice주소](https://github.com/imradhakrishnan/EmojiRecommender, "텍스트를 감정이 담긴 목소리로 변형해주는 프로그램입니다. 다른 github 주소로 연결됩니다.")
* 텍스트를 감정이 담긴 목소리로 변형해주는 프로그램입니다.
* 코드
  
  `demo_page.py`
  
  Streamlit를 사용해 텍스트를 음성으로 변환하는 데모 페이지를 구축.
  (Streamlit : 데이터 과학 및 웹 애플리케이션 개발을 위한 간단하고 빠르게 사용할 수 있는 Python 라이브러리)

  HTML 생성 및 Streamlit 페이지 설정:
  
  create_download_link: 다운로드 링크를 생성하는 함수.
  
  st.set_page_config: Streamlit 페이지의 설정을 지정하는 함수로, 페이지 제목과 아이콘을 설정.

  TacotronSTFT 및 관련 클래스:
  
  TacotronSTFT 클래스: Mel Spectrogram을 계산하기 위한 TacotronSTFT 클래스가 정의되어 있습니다. 이 클래스는 주어진 필터 길이, 합/건너뛰기 길이, 창 길이 등을 사용하여 Mel Spectrogram을 계산합니다.

  모델 및 기타 설정 로딩:
  
  미리 학습된 모델 및 기타 설정을 로드하는 함수와 변수들이 정의되어 있습니다.

  Streamlit UI 및 음성 합성:
  
  st.write 및 st.markdown: Streamlit을 사용하여 페이지에 텍스트 및 마크다운을 쓰는 함수.
  
  st.cache_resource: 모델 로딩에 대한 캐시를 설정하는 함수. 캐시는 모델을 한 번 로드하고 이후에는 재사용하여 성능을 향상시킵니다.
  
  get_models: 모델을 로드하는 함수.
  
  get_style_embedding: 스타일 임베딩을 얻는 함수.
  
  tts: 텍스트를 음성으로 변환하는 함수.
  
  new_line: Streamlit UI에서 사용자 입력을 받고 음성 합성을 시작하는 함수.

  Streamlit 페이지 구성:
  
  Streamlit을 사용하여 텍스트 입력, 스타일 선택 등의 UI 요소를 만들고, 사용자가 입력한 내용을 기반으로 음성을 합성하는 데 사용됩니다.


## Tweetvec2
>Emotivoice를 수정할 때 사용할 Tweetvec2가 어떤 역할을 하는 프로그램인지 설명하기 위해 작성했습니다.
>코드 수정 전에, 각 파일들이 어떤 역할을 하는지를 쉽게 알아보기 위해 간단한 설명을 첨부하는 역할을 합니다.
---
[tweetvec2주소](https://github.com/imradhakrishnan/EmojiRecommender, "이모티콘을 추천해주는 프로그램입니다. 다른 github 주소로 연결됩니다.")
* 이모티콘을 추천해주는 프로그램입니다.
* 코드
  
  `[average_main.py]`
  
  트윗 임베딩 :
  
   best_model 디렉토리에 저장된 모델을 사용하여 트윗을 임베딩하는 encode_char.py라는 스크립트를 호출.
   임베딩 결과를 (.npy 형식으로) CSV 파일로 변환.

  교차 검증용 데이터 분할 :
  
  k-폴드 교차 검증(k=5)을 사용하여 데이터를 교육 및 테스트 세트로 분할,
  분할된 데이터를 별도의 CSV 파일에 저장.

  이모지 임베딩 :
   EmojiEmbeddings.py라는 스크립트를 사용해 이모지를 임베딩.

  이모지 추천 :
   EmojiRecommender.py라는 스크립트를 사용하여 코사인 유사성, 랭킹 및 정확도 계산을 통해 이모지를 추천.

```
import subprocess
import csv
import numpy as np
import sklearn
import os
import sys
이전코드

->

변경 후 코드
```
