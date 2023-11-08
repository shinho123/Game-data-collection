# API(Open API, Rest API)란?

* API는 **Application Programming Interface**의 약자로 프로그램과 프로그램이 서로 데이터를 주고 받기 위해 사전에 정한 요청·응답 방식을 의미함

* 즉, 각자의 프로그램끼리 서로 일하는 방법을 약속한 것이라 해석하면 됨
  
# Open API

<img width="376" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/5151d926-434f-4db5-8df4-9b15f9400b65">

* open api(open application programming interface, open api)는 누구나 사용할 수 있도록 공개된 API를 의미함

* open api를 사용 시 데이터 기반의 모델 개발 시 비용을 절감할 수 있고 양질의 모델 개발을 수행할 수 있음

* open api는 카테고리 별로 분류를 하면 지도, SNS, 음악, 공공데이터, 비즈니스, 날씨, 쇼핑 등 우리 생활과 밀접한 다양한 분야에서 사용이 가능하며 데이터 제공처는 네이버, 다음, 카카오, 구글, 페이스북 뿐 아니라 정부에서도 그동안 수집한 공공데이터를 open api 형태로 제공하고 있음

* open api를 사용하기 위해서는 공개키를 필수적으로 발급 받아야함

# Rest API

<img width="475" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/e8b33c26-46cd-4542-861f-f886c5de7e54">

* Rest API는 HTTP 통신에서 어떤 자원에 대한 CRUD 요청을 Resource와 Method로 표현하여 특정한 형태로 전달하는 방식임
  1. HTTP URI(Uniform Resource Identifier)를 통해 자원을 명시
  2. HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD(Create, Read, Update, Delete) 연산을 수행
  3. 요청에 대한 Resource는 JSON/XML/HTML과 같은 특정한 형태로 표현 됨

# Riot API

<img width="910" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/a45e86da-f9e2-45c6-84dc-77d283a4292d">

* Riot Games API는 자체 애플리케이션이나 웹사이트를 구축하는데 사용할 수 있도록 개발자에게 제공되는 **REST API** 임

* 현재 모든 League of Legends API는 버전 4(2020년 3월 19일 업데이트)임
  
# Game-data-collection
* riot api 롤 게임 유저 정보 수집

* 수집 기간 : 2023.09.16 기준

* 데이터 정보 : 라이엇 게임즈 회사에서 제공하는 리그 오브 레전드(League of legend)게임의 유저 정보

* 수집 범위 : 아이언 ~ 그랜드마스터(Ⅰ~Ⅳ)
  
# 데이터 수집
```python
api_key = "RGAPI-69bc0d5a-5d4d-49e7-9175-8c12232b8abb" # 데이터 수집을 위해서는 별도의 api key를 생성해야함

request_header = {
                    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.164 Safari/537.36",
                    "Accept-Language": "ko,en-US;q=0.9,en;q=0.8,es;q=0.7",
                    "Accept-Charset": "application/x-www-form-urlencoded; charset=UTF-8",
                    "Origin": "https://developer.riotgames.com",
                    "X-Riot-Token": api_key
                }

request_header_match_v5 = {
                    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.203",
                    "Accept-Language": "ko,en;q=0.9,en-US;q=0.8",
                    "Accept-Charset": "application/x-www-form-urlencoded; charset=UTF-8",
                    "Origin": "https://developer.riotgames.com"
}
```
# API KEY

<img width="416" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/db3b5f87-590c-45ef-abd8-d5314d97db5e">

* 데이터 수집에 필요한 api_key 및 요청시 필요한 request header에 대한 정보를 저장함
* 위 그림처럼 API_KEY를 직접 발급받아야함

# 데이터 수집 함수(CHAMPION_MASTERY_V4, SUMMONER_V4, MATCH_V5)
```python
def Champion_mastery_v4(encryptedSummonerId):
    url = f"https://kr.api.riotgames.com/lol/champion-mastery/v4/champion-masteries/by-summoner/{encryptedSummonerId}?api_key=api_key"
    
    response = requests.get(url, headers=request_header)
    
    # Rate Limit 초과 오류를 처리
    if response.status_code == 429:
        retry_after = int(response.headers['Retry-After'])
        print(f"Rate Limit 초과, {retry_after}초 대기 후 재시도")
        time.sleep(retry_after)
        response = requests.get(url, headers=request_header)
    
    return response.json()

def summoner_v4_by_summoner_name(summonerName):
    # encodingSummonerName = parse.quote(summonerName)
    url = f"https://kr.api.riotgames.com/lol/summoner/v4/summoners/by-name/{summonerName}"
    
    # Rate Limit을 준수하기 위해 2초 대기
    time.sleep(2)
    
    response = requests.get(url, headers=request_header)
    
    # Rate Limit 초과 오류를 처리
    if response.status_code == 429:
        retry_after = int(response.headers['Retry-After'])
        print(f"Rate Limit 초과, {retry_after}초 대기 후 재시도")
        time.sleep(retry_after)
        response = requests.get(url, headers=request_header)
    
    return response.json()

def summoner_v4_by_encryptedSummonerId(summonerId):
    url = f"https://kr.api.riotgames.com/lol/summoner/v4/summoners/{summonerId}?api_key={api_key}"
    
    time.sleep(2)
    
    response = requests.get(url, headers=request_header)
    
    # Rate Limit 초과 오류를 처리
    if response.status_code == 429:
        retry_after = int(response.headers['Retry-After'])
        print(f"Rate Limit 초과, {retry_after}초 대기 후 재시도")
        time.sleep(retry_after)
        response = requests.get(url, headers=request_header)
        
    return response.json()

def summoner_v4_by_encryptedSummonerId_champion(summonerId):
    url = f"https://kr.api.riotgames.com/lol/champion-mastery/v4/champion-masteries/by-summoner/{summonerId}?api_key={api_key}"
    
    time.sleep(2)
    
    response = requests.get(url, headers=request_header_match_v5)
    
    # Rate Limit 초과 오류를 처리
    if response.status_code == 429:
        retry_after = int(response.headers['Retry-After'])
        print(f"Rate Limit 초과, {retry_after}초 대기 후 재시도")
        time.sleep(retry_after)
        response = requests.get(url, headers=request_header_match_v5)
        
    return response.json()

def match_v5_puuid(puuid):
    url =f"https://asia.api.riotgames.com/lol/match/v5/matches/by-puuid/{puuid}/ids?start=0&count=5&api_key={api_key}"
    
    time.sleep(2)
    
    response = requests.get(url, headers = request_header_match_v5)
    
    # Rate Limit 초과 오류를 처리
    if response.status_code == 429:
        retry_after = int(response.headers['Retry-After'])
        print(f"Rate Limit 초과, {retry_after}초 대기 후 재시도")
        time.sleep(retry_after)
        response = requests.get(url, headers=request_header_match_v5)
        
    return response.json()

def match_v5_matchid(matchid):
    url = f"https://asia.api.riotgames.com/lol/match/v5/matches/{matchid}?api_key={api_key}"
    
    time.sleep(2)
    
    response = requests.get(url, headers = request_header_match_v5)
    
    # Rate Limit 초과 오류를 처리
    if response.status_code == 429:
        retry_after = int(response.headers['Retry-After'])
        print(f"Rate Limit 초과, {retry_after}초 대기 후 재시도")
        time.sleep(retry_after)
        response = requests.get(url, headers=request_header_match_v5)
        
    return response.json()

def get_all_league_exp_v4_data(api_key, region='kr'):
    
    base_url = f'https://{region}.api.riotgames.com/lol/league-exp/v4/entries/'

    queues = ['RANKED_SOLO_5x5']
    # ['IRON', 'BRONZE', 'SILVER', 'GOLD', 'PLATINUM', 'DIAMOND']
    tiers = ['IRON', 'BRONZE', 'SILVER', 'GOLD', 'PLATINUM', 'DIAMOND', 'MASTER', 'GRANDMASTER', 'CHALLENGER']
    
    divisions = ['I', 'II', 'III', 'IV']

    all_data = []

    for queue in queues:
        for tier in tiers:
            for division in divisions:
                page = 26
                while page <= 70:  # 각 디비전 페이지를 10페이지까지만 추출
                    print(f'Fetching {queue}/{tier}/{division} Page {page}...')
                    params = {
                        'api_key': api_key,
                        'page': page,
                        'pageSize': 200,  # 페이지당 항목 수
                    }
                    response = requests.get(f'{base_url}{queue}/{tier}/{division}', params=params)

                    if response.status_code == 429:
                        # Rate Limit 초과 시 대기
                        retry_after = int(response.headers['Retry-After'])
                        time.sleep(retry_after)
                        continue

                    data = response.json()
                    all_data.extend(data)

                    if len(data) < 200:
                        break
                    else:
                        page += 1

    return all_data
```
* 데이터를 불러오기 위해 필요한 정보를 불러올 수 있는 함수를 개별적으로 구성하였음
  * champion_mastery_v4 : 유저가 보유한 챔피언 정보
  * match_v5 : 유저가 플레이한 게임 매치 데이터
  * summoner_v4 : 유저의 활동 데이터
    
* 같은 함수라도 입력되는 매개변수가 다른 경우가 있는데 이는 **데이터를 불러올 때 서로 요구하는 유저 데이터가 모두 다르기** 떄문에 상황에 따라 데이터를 불러올 수 있도록 하나의 함수라도 여러개의 매개변수 타입으로 구성하였음

# 데이터 정제
  ## 데이터가 raw 형태이므로 일련의 정제 과정을 통해 datas 변수에 데이터를 저장함
```python
datas = []

for idx, st in enumerate(all_data):
    u_data = {}
    try:
        u_data['user_name'] = st['summonerName']
        u_data['play_num'] = st['wins'] + st['losses']
        u_data['win_rate'] = 100 * (st['wins'] / (st['wins'] + st['losses']))
        u_data['veteran'] = st['veteran']
        u_data['hot_streak'] = st['hotStreak']
        u_data['freshblood'] = st['freshBlood']
        u_data['league_points'] = st['leaguePoints']
        u_data['summonerId'] = st['summonerId']
        u_data['inactive'] = st['inactive']
        u_data['tier'] = st['tier']
        u_data['summonerId'] = st['summonerId']
        datas.append(u_data)
        
    except Exception as e:
        print(st)
        continue
```

# 데이터 정보 확인

```python
print(datas)
```

<img width="590" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/ab618ef4-426f-41b6-8081-9e1378cb628a">

# 데이터프레임 형태로 확인

```python
df = pd.DataFrame(datas)
```
<img width="592" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/2181fb9c-8a89-429b-a387-d63cadde74a5">

# csv 파일로 저장

```python
df.to_csv('riot_games_user_data.csv')
```

# 저장된 csv 파일 재확인

<img width="835" alt="image" src="https://github.com/shinho123/Game-data-collection/assets/105840783/881dcc58-9e8f-4ec7-ab3a-bf6dec4b667b">




# 추후 연구 계획

* 추가적인 유저 데이터를 더 확보하여 유저의 게임 승리 예측이나 게임에서 이탈하는 이탈 유저 예측 분석을 수행 예정임

* 또한 게임 내에서 유저가 플레이한 정보를 바탕으로 게임 내의 추세를 분석할 수도 있음 이를 통해 기업에서는 유저가 실제로 요구하거나 필요로 하는 정보들을 명확하게 파악할 수 있음

