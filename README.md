# 딥러닝을 활용한 화장품 성분분석& 제품인식서비스
## 1. 주관
한국데이터산업진흥원<br />


## 2. 실시
경희대빅데이터연구소<br />


## 3. 프로젝트 구성도
### (1) 팀 이름
#### MSG
![팀이름 소개](https://user-images.githubusercontent.com/49123169/65418701-fc5c8480-de37-11e9-8551-dc134204b4be.PNG)<br />



### (2) 팀장 및 팀원
|팀장 및 팀원|이름|
|-------|-------------------------------------------|
|팀장|신동준|
|팀원|박주환, 노주영, 김수헌, 정예림, 김소은, 김준형|<br />




### (3) 각 구성원 역할
|역할|이름|
|----------------------------------------|-------------------------------------------|
|DB 구축 및 웹사이트 구축|신동준|
|웹사이트 구축 및 웹사이트 디자인|김준형|
|성분 크롤링 및 인스타크롤링, 모델링|박주환|
|화장품 크롤링 및 전처리, 발표|정예림|
|화장품 크롤링 및 전처리, PPT제작|김소은|
|화장품 크롤링 및 전처리, PPT제작|김수헌|
|화장품 크롤링 및 전처리, PPT제작, 모델학습|노주영|

### (4) 시스템 아키텍처
![시스템 아키텍처2](https://user-images.githubusercontent.com/49123169/72203117-f9e76e80-34aa-11ea-9a89-6bc2eb66af84.PNG)
* AWS 환경에서 해당 프로젝트를 진행했습니다.

|System|사용|
|----------------------------------------|-------------------------------------------|
|AWS 사용환경| Amazon SageMaker|
|WEB |Flask|
|Deep Learning|Keras|
|DataBase|MySQL|
|Data preprocessing|OpenCV, Pandas, NumPy, PIL|
|Crawling|Selenium, BeautifulSoup|


### (5) 화장품 서비스
#### * 이름 : 화분
![화분](https://user-images.githubusercontent.com/49123169/65417295-b18d3d80-de34-11e9-839e-7aa1304072ad.png)

#### * 작동 방법
![시스템 아키텍처](https://user-images.githubusercontent.com/49123169/72203031-ceb04f80-34a9-11ea-8dcf-44019f948cd4.PNG)
* 휴대폰으로 화장품을 사진으로 촬영하여 이미지를 업로드합니다. 
* 업로드된 이미지를 분석하여 정확한 제품을 인식하고 그 제품의 화장품 성분을 찾아 매칭시킵니다.
* 매칭시킨 정보를 사용자에게 바로 알려주는 순서로 진행됩니다.

## 4. 프로젝트 목표
1. 사회적 목표 : 각 화장품에 대한 성분을 제대로 알려주어 사람들이 자신에게 맞는 화장품을 쓰게 만들기 위해서입니다.
2. 팀적 목표 : 자신이 할 수 있는 프로그램에 대한 더 깊은 공부 및 복습, 해당 교육으로 배운 CNN을 구현하는데 목표를 두었습니다.

## 5. 프로젝트 진행
### (1) 데이터 수집 및 전처리
(1) 화장품 성분 수집(성분 등급표)
* 화장품 성분에 대한 등급을 수집하기 위해 EWG라는 미국의 비영리 단체의 자료를 수집했습니다.
![ewg 그림](https://user-images.githubusercontent.com/49123169/72205661-3f1a9900-34c9-11ea-894d-0e913900601d.png)

* 이 사이트에는 1~10등급까지 성분에 대한 등급표가 나오기 때문에 저희 프로젝트에 적합한 자료라고 생각하여 크롤링을 통해 자료를 수집했습니다. 크롤링으로는 *Selenium*과 *Beautifulsoup*을 사용했습니다. 
  * *Selenium*을 사용한 이유는 ewg의 사이트에서 성분을 수집하기 위해선 화장품을 클릭한 후에 성분을 수집해야 합니다. 동적 크롤링에서는 selenium이 최적이기 때문에 크롤링 속도가 느리더라고 이 방법을 사용했습니다.
  * *Beautifulsoup*을 사용한 이유는 화장품 성분 등급표를 가지고 오려고 했기 때문입니다. 성분 등급표가 이미지파일로 되어 있기 때문에 이를 숫자만 가지고 오기 위해서는 이미지 링크를 통해 얻어야 했습니다. selenium의 경우 링크에 대한 정보를 가져오는 것이 힘들었습니다. 그에 비해 beautifulsoup은 링크 가져오는 것이 꽤 쉬웠고 분리하는 과정도 쉬웠습니다. 그래서 등급표만 beautifulsoup을 사용했습니다.

~~~
#크롤링을 위해 짠 코드
for j in range(0,10000,10):
    driver.implicitly_wait(randint(2,4))
    driver.get("""https://www.ewg.org/skindeep/browse.php?category=after_sun_product&&showmore=products&start={0}""".format(j))
    for i in range(2,12):
        driver.find_element_by_xpath("""//*//*[@id="table-browse"]/tbody/tr[{0}]/td[3]/a""".format(i)).click()
        a_element=driver.find_elements_by_xpath("//td[@class='firstcol'] |a[@href]")
        ic=[]
        for i in range(len(a_element)):
            ic.append(a_element[i].text)
        ic=[x for x in ic if x]
        span_element=driver.find_elements_by_xpath("//td[@width]  |span[@style]")
        sb=[]
        for i in range(len(span_element)):
            sb.append(span_element[i].text)
        sb=[x for x in sb if x]
        div_element=driver.find_elements_by_xpath("//td[@align]  |div[@*]")
        ssb=[]
        for i in range(len(div_element)):
            ssb.append(div_element[i].text)
        ssb=[x for x in ssb if x]
        html=driver.page_source
        soup=BeautifulSoup(html,'html.parser')
        data1_list=soup.findAll('div',{'id':"prod_cntr_score"})
        li_list=[]
        for data1 in data1_list:
            li_list.extend(data1.findAll('img'))
        li_list2=[]
        for x in li_list:
            li_list2.append(str(x))
        li_list3=[]
        for y in range(len(li_list2)):
            li_list3.append(li_list2[y][68:69])
        ic=pd.Series(ic,name='ingredient')
        sb=pd.Series(sb,name='concerns')
        ssb=pd.Series(ssb,name='score')
        li_list=pd.Series(li_list3,name='score_number')
        first=pd.concat([ic,sb,ssb,li_list],axis=1)
        ewg=ewg.append(first)
        driver.back() 
 ~~~

(2) 화장품 성분 수집(분류할 화장품 성분) 
* 7개의 브랜드(CHANEL, BOBBI BROWN, ESTEE LAUDER, ETUDE HOUSE, innisfree, MISSHA, Dr.Jart), 
  5개의 제품군(스킨,로션, 선크림, 클렌징, 크림)을 사용하여 33개의 화장품을 분류할 계획을 세웠습니다.
* 그래서 해당 제품의 성분들을 추출하기 위해서 각 홈페이지에 들어가 크롤링 작업을 실시했습니다.
* 해당 작업은 다른 팀원들이 진행하여 코드를 가지고 있지 않습니다.

  
(3) 화장품 이미지 크롤링(구글 이미지) 
* 33개의 화장품 사진을 학습시킬 이미지를 구하기 위해 첫 번째 방법으로 구글을 사용했습니다.
   구글 검색을 통해 얻을 수 있는 이미지를 추출했습니다.
  (제 담당이 아니라서 코드가 없습니다.)
  
(4) 화장품 이미지 크롤링(인스타그램)
* 학습시킬 화장품 사진의 이미지가 매우 부족하여 두 번째 방법으로 인스타그램 크롤링을 진행했습니다.
* https://github.com/huaying/instagram-crawler 해당 깃허브에 있는 방법을 가지고 살짝 튜닝 과정을 거쳐 크롤링을 진행했습니다.

