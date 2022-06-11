# [Crawling - KCIA](/crawl/kcia.ipynb) (Result File: [ing_name.csv](/data/ing_name.csv)) <br>
대한 화장품 협회 웹사이트에서 제공하는 '성분사전'에서 성분정보 수집하기<br>
https://kcia.or.kr/cid/main/

### [ 목적 ]
- 최종적으로 제품 및 주성분 목록을 수집 시 활용할 사이트: InciDecoder
- InciDecoder 웹사이트에서 성분 영문명을 사용하여 특정 성분 페이지에 직접 접근 가능
- InciDecoder 성분 페이지 접근에 활용할 화장품 성분의 영문명을 KCIA 웹사이트에서 수집 


### [ KCIA 웹사이트 구성 및 특징 ]
1. 화장품 성분 목록이 1~2038개 페이지로 구성
   
2. 페이지 접근 url 에서 페이지 숫자{num}로 특정 페이지에 직접 접근 가능
   https://kcia.or.kr/cid/search/ingd_list.php?page={num}
   
3. 각 페이지에서 하기 항목들을 html table 형식으로 제공<br>

   |성분코드|성분명|영문명|CAS No|구명칭|
   |---|---|---|---|---|

### [ 데이터 수집 방법 - <span style='color:green'>Tool</span> ]
1. 수집할 항목들을 컬럼명으로 지정하여 빈 데이터 프레임 생성데이터 프레임 명:'ing_df' - <span style='color: green'>pandas</span>
   
2. 1~2038 페이지에 url로 접근 - <span style='color: green'>requests</span>
   
3. 각 페이지 html 불러오기 - <span style='color: green'>BeautifulSoup</span>
   
4. html 소스에 접근하여 class가 bbs_list인 테이블을 받아오기 - <span style='color: green'>BeautifulSoup</span>,<span style='color: green'>parser_functions.make2d</span>
   
5. 각 테이블에서 수집한 정보를 임시 데이터 프레임으로 담은 후, 사전에 생성한 'ing_df' 데이터에 누적 (concat)

### [ 수집한 raw data 형태 ]

||성분코드|성분명|영문명|CAS No|구명칭|
|---|---|---|---|---|---|
|1|5|류신|Leucine|328-39-2(DL-)\r\n61-90-5(L-)|---|
|2|4|루핀아미노산|Lupine Amino Acids|---|---|
|3|3|구멍쇠미역추출물|Agarum Cribrosum Extract|---|---|
|4|2|가지열매추출물|Solanum Melongena (Eggplant) Fruit Extract|84012-19-1|가지추출물|---|---|
|5|1|가공소금|---|---|---|
|...|...|...|...|...|....|

### [ 데이터 확인 및 전처리 내용 ]
- 성분코드의 컬럼의 마지막 행 번호와 데이터 프레임의 행 개수 상이<br>(성분코드 마지막 행 번호:22996 vs 데이터 프레임의 행 개수: 20376)<br>
  
  <span style='color:red'>&rarr;</span> set 자료구조의 차집합을 통하여 빠진 성분코드 번호가 있는지 확인<br>
   <span style='color:red'>&rarr;</span> 수집한 데이터의 성분코드 번호에 누락된 번호가 있음<br>

- 수집 목적에 따라 영문명을 제공하지 않는 경우 사용 결측치로 판단
   |컬럼|계|
   |---|---|
   |성분명|20,376|
   |영문명|19,414|<br>

- 수집 목적에 따라 KCIA에 기재된 성분 영문명을 InciDecoder에서 제공하는 형식으로 전처리 필요:
  - 식물의 학명 뒤에 따라오는 괄호안 일반 명칭 삭제<ve>
  - 전체 소문자 처리
  - 공백 및 "/" 문자를 "-"로 변경
  
### [ 최종 파일 및 특징 ] 
파일명: ing_name.csv

|성분코드|성분명|영문명|formatted_영문명|
|---|---|---|---|
|1|가공소금|NaN|NaN|
|2|가재열매추출물|Solanum Melongena (Eggplant) Fruit Extract|solanum-melongena -fruit-extract|
|...|...|...|...|
|6|류코노스톡/무발효여과물|Leuconostoc/Radish Root Ferment Filtrate|leuconostoc-radish-root-ferment-filtrate|

- KCIA 웹사이트에서 수집한 성분 데이터 수: 20376개
- 결측치 (영문명을 제공하지 않는 성분) 제거를 통해 최종적으로 수집한 성분 데이터 수: 19414개
