Jupyter Notebook과 판다스를 사용하여 데이터 정제
=============

excel 파일 읽어오기
-------------
```
food_nutrients = pd.read_excel('통합 식품영양성분DB_20211106.xlsx', skiprows=[0,1,2], engine='openpyxl')
```
skiprows를 통해 필요없는 행 제외하고 읽어오기 가능, 엑셀파일의 경우 engine 부분을 써주어야 한다.

csv 파일 읽어오기
-------------
```
df = pd.read_csv('food_cleaning.csv', sep='\t', engine='python')
```
engine='python'을 해주면 한글 파일이 깨지지않고 읽어온다.

필요없는 column 제거
-------------
```
df.drop(['','',''], axis=1)
```
axis=1을 해주어야 column을 제거해 주는것이며 제거해야할 column이 여러개라면 리스트로 넣어줘어야 한다.

결치값 제거(replace), 중복행 제거(drop_duplicates), 타입 변경(astype)
-------------
```
df.loc[:, '':''].replace(기존 데이터, 바꿀 데이터)

df.drop_duplicates([''])

df.astype({'바꿀 컬럼':'타입 명'})
df.loc[:, '':''].astype(타입)
```
.loc 및 .iloc의 경우 행은 리스트의 index 0, 열은 리스트 index 1에 적어준다.
위 코드는 열을 기준으로 연산을 함으로 index 1에 column명 작성 

replace에서 기존 데이터가 여러개라면 리스트를 사용해서 한번에 바꿀 수도 있고
re.compile()을 통해 정규 표현식으로 사용할 수 있다.

astype의 경우 한 가지라면 딕셔너리 형태로 여러개라면 loc를 사용해 한 번에 바꿀 수 있다.

단위 통일 방법
-------------
```
gTomg = 0.001

for col in df.loc[: ,'단백질(g)':'카페인(㎎)']:
    if '㎎' in col:
        df[col] = df[col] * gTomg
```
데이터 프래임의 경우 for문을 하면 컬럼명으로 iterator한다.
그렇기 때문에 변경할 column을 if 문으로 위와 같이 선택 가능

각 column에 대한 행 연산 코드
-------------
```
for name in have_percent:
    name = name[:-3]
    for i in range(1, len(df).index)):
        if df.loc[i, name + '(g)'] == 0:
            df.loc[i, name+ '(g)'] = \
                df.loc[i, name + '(%)'] * df.loc[i, '1회제공량'] / 100
```
column을 첫 번째 for문으로, 행을 두 번째 for 문으로 하여 컬럼의 행 별로 연산이 가능하다.


데이터프레임, Series 연산자
-------------
```
for nutrien in integrated_G_MG:
    df[nutrien+'(g)'] = df[nutrien+'(g)'].add(df[nutrien+'(㎎)'])

df = df[(df['단위']=='mL')| ((df['1회제공량']<=3000) & (df['단위']=='g'))]
```
add 뿐만 아니라 sub, div, mul 모두 가능하며 sum, mean등의 집계함수도 사용 가능  
논리연산자도 사용가능 


index초기화(reset_index)
-------------
```
df.reset_index(drop=True)
```

특정 컬럼만 사용, 반올림 함수, 정렬
-------------
```
df = df.loc[:, ['', '', '', '',]]

df.round(6)

df.sort_values(['비타민 D2'], ascending=False)[['식품명', '비타민 D2']][:50]
```
컬럼명만 같다면 순서에 상관없이 자동으로 컬럼을 찾아서 해당 컬럼을 가지고 와준다.  
round함수의 경우 몇 번째 자리에서 반올림해줄건지 숫자로 적어줘서 사용  

csv파일 저장
-------------
```
df.to_csv("food_min_columns.csv", mode='w', sep='\t')


import csv

with open('food_name_list.csv','w',encoding='utf-8' ) as file :
    write = csv.writer(file)
    write.writerow(df['식품명'].tolist())

```
데이터 프레임의 경우 to_csv를 사용해서 저장 sep 옵션을 통해 구분인자 지정(default 값은 ',')  
리스트의 경우 csv라이브러리 사용해서 저장








