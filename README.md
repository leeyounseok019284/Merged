# 선택 감정과 이모티콘
## 제작의도
- 저희가 어떤 생각으로 이 프로젝트를 진행하였는지, 그리고 어떤 프로그램이 목표인지를 설명하기 위해 작성했습니다.
- 코드 완성 전까지는 계획이 바뀔 때마다 수정된 부분을 확인할 수 있는 곳으로 사용하기 위해 필요한 구간입니다.
----
TtS가 어색하게 느껴지는 이유 중 가장 큰 것은, 감정을 표현할 수 없는 목소리라는 점이라고 생각합니다. 하지만 이런 단점을 보완할 감정 추가라는 기능이 있는 프로그램이 있었기에, 그 프로그램의 특징을 활용하여 더 매력적인 프로그램으로 만들 수 있었으면 좋겠다고 생각했습니다.
때문에 emotivoice의 코드와 tweetvec2의 코드를 적절히 활용해서, 감정이 담긴 목소리를 생성할 때 함께 사용할 적절한 이모티콘을 추천해주는 프로그램을 만들 계획입니다.

## 프로그램 실행
- 프로그램을 실행했을 때의 모습을 간단하게 보여주기 위해 작성했습니다.
- 어떤 프로그램인지를 명시적으로 보여주기 위해 필요한 구간입니다.
---
![이미지 설명](영상대체예정.jpg)
영상, 스크린샷을 올릴 예정입니다.

## Emotivoice
- 메인이 될 프로그램인 Emotivoice가 어떤 역할을 하는 것인지를 설명하기 위해 작성했습니다.
- 코드 수정 전에, 각 파일들이 어떤 역할을 하는지를 쉽게 알아보기 위해 간단한 설명을 첨부하는 역할을 합니다.
---
[emotivoice주소](https://github.com/imradhakrishnan/EmojiRecommender, "텍스트를 감정이 담긴 목소리로 변형해주는 프로그램입니다. 다른 github 주소로 연결됩니다.")
* 텍스트를 감정이 담긴 목소리로 변형해주는 프로그램입니다.


## Tweetvec2
- Emotivoice를 수정할 때 사용할 Tweetvec2가 어떤 역할을 하는 프로그램인지 설명하기 위해 작성했습니다.
- 코드 수정 전에, 각 파일들이 어떤 역할을 하는지를 쉽게 알아보기 위해 간단한 설명을 첨부하는 역할을 합니다.
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
path = os.path.abspath(__file__)
mainpath = os.path.dirname(path)
sys.path.append(mainpath + '/modified_tweet2vec/tweet2vec/')
import encode_char as ec
import npload as npl
sys.path.append(mainpath + '/RecommendEmojis/')
import EmojiEmbeddings as ee
import EmojiRecommender as er
from sklearn.model_selection import KFold
#Call preprocessing - the shell script calls two preprocessing scripts
'''
전처리:
- preprocessing.sh라는 셸 스크립트를 호출하며, 해당 스크립트에서 두 가지 전처리 스크립트를 호출.
(preprocessing.ch = parent_path=$( cd "$(dirname "${BASH_SOURCE[0]}")" ; pwd -P )
cd "$parent_path"
cd modified_tweet2vec/tweet2vec/ &&
python data_process.py $parent_path'/modified_tweet2vec/data/input.csv' $parent_path'/modified_tweet2vec/data/input_preprocessed_1.csv' &&
cd $parent_path"/modified_tweet2vec/misc/" &&
python preprocess.py $parent_path'/modified_tweet2vec/data/input_preprocessed_tweet_1.csv' $parent_path'/modified_tweet2vec/data/input_preprocessed_2.csv')
- 서브프로세스 호출의 반환 코드를 확인하여 성공적인 실행을 확인.
'''
print "Start Preprocessing"
if subprocess.call("./preprocessing.sh") == 0:
    print "Done preprocessing"
    #Call script to embed tweets
    print "Begin encoding"
    inputpath = mainpath + "/modified_tweet2vec/data/input_preprocessed_2.csv"
    modelpath = mainpath + "/modified_tweet2vec/tweet2vec/best_model/"
    outputpath = mainpath + "/modified_tweet2vec/tweet2vec/result"
    args = []
    args.append(inputpath)
    args.append(modelpath)
    args.append(outputpath)
    ec.main(args)
    print "Done encoding“
'''
트윗 임베딩:
- best_model 디렉토리에 저장된 모델을 사용하여 트윗을 임베딩하는 encode_char.py라는 스크립트를 호출.
- 임베딩 결과를 (.npy 형식으로) CSV 파일로 변환.
'''
    #Converts output of encoding (.npy) to .csv
    print "Begin converting .npy to .csv"
    npypath = mainpath + "/modified_tweet2vec/tweet2vec/result/embeddings.npy"
    prepath = mainpath + "/modified_tweet2vec/data/input_preprocessed_2.csv"
    csvpath = mainpath + "/modified_tweet2vec/data/embeddings.csv"
    nplargs = []
    nplargs.append(npypath)
    nplargs.append(prepath)
    nplargs.append(csvpath)
    npl.main(nplargs)
 #.npy에서 .csv로 변환:
 #임베딩 단계의 출력(.npy 형식)을 CSV 파일로 변환합니다.
    print("Finished converting .npy to .csv")
    #Splits data for cross validation
    print "Start splitting data"
    embed_data = mainpath + "/modified_tweet2vec/data/embeddings.csv"
    with open(inputpath, "rb") as inputfile:
        inputdata = csv.reader(inputfile, delimiter=",")
        inputdata = np.asarray(list(inputdata))
        with open(embed_data, "rb") as csvfile:
            data = csv.reader(csvfile, delimiter=',')
            data = list(data)
            kf = KFold(n_splits=5)
            kf.get_n_splits(data)
            i = 1
            indices = kf.split(data)
            print indices
            for train_index, test_index in indices:
                data_train = []
                data_test = []
                for ti in train_index:
                    data_train.append(data[ti])
                for test_i in test_index:
                    data_test.append(data[test_i])
                main_data_train, main_data_test = inputdata[train_index], inputdata[test_index]
                trainfile = mainpath + "/modified_tweet2vec/data/train"
                testfile = mainpath + "/modified_tweet2vec/data/test"
                trainfile = trainfile + str(i) + ".csv"
                testfile = testfile + str(i) + ".csv"
                main_trainfile = mainpath + "/modified_tweet2vec/data/main_train"
                main_testfile = mainpath + "/modified_tweet2vec/data/main_test"
                main_trainfile = main_trainfile + str(i) + ".csv"
                main_testfile = main_testfile + str(i) + ".csv"
                with open(trainfile, 'w') as traincsv:
                    writer = csv.writer(traincsv)
                    writer.writerows(data_train)
                with open(testfile, 'w') as testcsv:
                    writer = csv.writer(testcsv)
                    writer.writerows(data_test)
                with open(main_trainfile, 'w') as main_traincsv:
                    writer = csv.writer(main_traincsv)
                    writer.writerows(main_data_train)
                with open(main_testfile, 'w') as main_testcsv:
                    writer = csv.writer(main_testcsv)
                    writer.writerows(main_data_test)
                i += 1
    print "Done splitting"
    #Maps the embedding from the train set to the emoji corpus
    print "Begin emoji embedding"
    for i in range(1,6):
        train_data = mainpath + "/modified_tweet2vec/data/train"
        train_data = train_data + str(i) + ".csv"
        emoji_embed_output = mainpath + "/modified_tweet2vec/data/emoji_embeddings"
        emoji_embed_output = emoji_embed_output + str(i) + ".csv"
        emoji_embed_args = []
        emoji_embed_args.append(train_data)
        emoji_embed_args.append(emoji_embed_output)
        ee.main(emoji_embed_args)
        #Recommends emojis - does cosine similarity, ranking and accuracy calculation
        recommend_output =  mainpath + "/modified_tweet2vec/data/RecommendedEmojis"
        recommend_output = recommend_output + str(i) + ".csv"
        test_data = mainpath + "/modified_tweet2vec/data/test"
        test_data = test_data + str(i) + ".csv"
        main_testfile = mainpath + "/modified_tweet2vec/data/main_test"
        main_testfile = main_testfile + str(i) + ".csv"
        recommend_args = []
        recommend_args.append(emoji_embed_output)
        recommend_args.append(recommend_output)
        recommend_args.append(test_data)
        recommend_args.append(main_testfile)
        er.main(recommend_args)
print "end"
```
