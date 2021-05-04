# Youtube LDA Modeling
>ICT COG AI Academy 고급과정(언어) Project

## 📆 개발 기간
2021.04.07 - 2021.04.19

## 📚 사용 기술
* Python
*	Konlpy
*	TF-IDF
*	LDA
*	WordCloud

---

## 1. 개요
유튜브 API, 정규표현식, Konlpy, LDA, WordCloud 등을 사용하여 데이터 수집, 전처리, 모델링과 시각화를 진행하고 추출한 키워드를 바탕으로 캐릭터의 이미지를 파악하고자 한다.   

---

## 2. 수행 내용

### 2-1. 데이터 수집
![crawling](https://user-images.githubusercontent.com/81695614/117000303-d689a800-ad1b-11eb-86ee-159ff9d90d14.jpg)   
유튜브 API를 사용하여 채널의 고유 id, 컨텐츠의 고유 id, 영상의 고유 id를 추출하였다.   
이 중 제목에 ‘[B대면데이트]#’이 들어간 영상이 각 캐릭터들의 정규 영상이므로 이 문장이 포함된 영상들에서만 댓글을 추출하였다. 이때 댓글은 한 번에 100개씩만 추출되기 때문에 ‘nextPageToken’이라는 파라메타를 이용하여 해당 파라메타가 없을 때까지 추출을 반복하였다. 댓글을 작성한 영상 제목, 댓글과 작성자, 그리고 좋아요 수를 수집하였다. 댓글은 총 64,400개가 추출되었다.

### 2-2. 데이터 전처리
![prep](https://user-images.githubusercontent.com/81695614/117000362-ebfed200-ad1b-11eb-9123-f8a8c3b4e7cd.jpg)   
수집한 댓글을 캐릭터 별로 구분하여 모델링을 진행할 수 있도록 영상 제목에서 캐릭터 명을 추출하여 새로운 열에 넣어주었다. 댓글에서 숫자나 문자를 제외한 요소를 없애기 위해 정규표현식을 사용하였다. ‘&lt;br />’이나 ‘<a …> </a>’ 등과 같은 html 태그들도 삭제해주었다. Konlpy의 Komoran을 사용하여 전처리된 댓글의 토큰화와 품사 태깅을 진행하였다. 품사 중 시청자들의 반응과 캐릭터의 이미지를 표현하는 감탄사와 형용사, 일반명사, 고유명사, 외국어 그리고 유효한 단어들을 얻고자 단어 수가 2 이상인 단어들만 추출하였다.

### 2-3. 데이터 모델링
모델링은 LDA로 진행하였다.   
LDA는 토픽 수를 직접 정해줘야 하는 특성이 있다. 최적의 파라메타 값을 찾고자 GridSearchCV를 사용하였다. 모델링 된 데이터의 Log-likelihood와 Perplexity를 계산하여 n_components와 max_iter 값에 따른 최적의 값을 찾아냈다.

<div>
<img src="https://user-images.githubusercontent.com/81695614/117001915-e1ddd300-ad1d-11eb-8ed3-2c6fb02af198.jpg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117001804-c5419b00-ad1d-11eb-9f37-33f3c4442d9d.jpg" width="45%" height="45%"/>
</div>

n_components 값을 2에서 100으로 줬을 때의 그래프에서 Log-likelihood가 클수록, perplexity가 작을 수록 최적의 상황이지만 각각의 그래프가 단조 감소, 증가할 뿐 유의미하진 않았다.   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117001695-9deace00-ad1d-11eb-95ac-3b4d48f7aafd.jpg" width="30%" height="30%"/>
<img src="https://user-images.githubusercontent.com/81695614/117001697-9e836480-ad1d-11eb-98f9-64aa81a468db.jpg" width="30%" height="30%"/>
<img src="https://user-images.githubusercontent.com/81695614/117001700-9f1bfb00-ad1d-11eb-9e79-ff75675254d6.jpg" width="30%" height="30%"/>   
</div>

그래서 pyLDAvis를 사용해 토픽 수에 따른 토픽의 크기를 비교하였다.   
5일 때는 토픽의 크기가 너무 크고 20일 때는 토픽의 크기가 너무 작아서 10으로 결정하였다.   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117001914-e1453c80-ad1d-11eb-863e-3721291e3b93.jpg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117001917-e1ddd300-ad1d-11eb-8457-36aeab1fd43d.jpg" width="45%" height="45%"/>
</div>

이 프로젝트에서 learning decay는 모델 학습에 영향을 주지 않는 것으로 나타났다. 다만 max_iter 값은 약 30까지 개선이 나타났기 때문에 최적의 값을 30으로 정했다. 따라서 n_components는 10, max_iter는 30, learning_decay는 기본값으로 두어 모델링을 실시하였다.   

### 2-4. 데이터 시각화
모델링을 시행한 결과를 바탕으로 캐릭터 별로 pyLDAvis를 사용한 시각화와 토픽 별 상위 30개의 키워드를 선정하여 WordCloud로의 시각화를 진행하였다.   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117006375-51a28c80-ad23-11eb-8ebd-f24ffa572d4a.jpeg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117003708-28343180-ad20-11eb-8114-0d7dc23ae1d0.png" width="45%" height="45%"/>
</div>

방재호   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117006377-52d3b980-ad23-11eb-8fc5-33febafe5fb5.jpeg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117003710-28ccc800-ad20-11eb-89a1-c852e340e396.png" width="45%" height="45%"/>
</div>

이호창   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117006379-536c5000-ad23-11eb-9712-b5c2e53edfb8.jpeg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117003713-29655e80-ad20-11eb-8eec-f5ca2cf12a4a.png" width="45%" height="45%"/>
</div>

임플란티드 키드   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117006381-536c5000-ad23-11eb-88df-0cfa9b04dc08.jpeg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117003701-266a6e00-ad20-11eb-80f9-b27bd1040914.png" width="45%" height="45%"/>
</div>

차진석   

<div>
<img src="https://user-images.githubusercontent.com/81695614/117006383-5404e680-ad23-11eb-9058-d9527d9a64e2.jpeg" width="45%" height="45%"/>
<img src="https://user-images.githubusercontent.com/81695614/117003705-279b9b00-ad20-11eb-86dc-f3252abf068b.png" width="45%" height="45%"/>
</div>

최준   
