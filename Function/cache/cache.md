-----
### cahce
-----
1. 자주 사용되는 데이터를 빠르게 불러올 수 있도록 저장하는 기능 : 이를 통해 의존성 설치 시간 단축
2. Github Marketplace에 정의된 공식 액션
3. 실습 : React 기반의 애플리케이션 사용 (github-actions-setting Repository Fork - my-app 디렉토리 사용)
<div align="center">
<img src="https://github.com/user-attachments/assets/c929042f-77a8-4004-af1a-021a712c8aa5">
</div>

   - 직접 : Create React App 검색하고, 커맨드를 실행해 구성
   - npm-start 커맨드를 통해 컴파일에 성공하면 my-app 애플리케이션을 웹 브라우저상에서 확인 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/3deba4a1-f3c6-4c67-beaa-9249ce1c7179">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/399b98e3-400b-4111-bb5e-5fd6fdfd60de">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6e0bff25-d61f-4e80-a977-e937c6006fde">
</div>

   - cache라는 이름의 Workflow 생성
```yaml
name: cache
on:
  push:
    paths:
      - "my-app/**" # my-app 디렉토리 내 변경 사항에 대해 모든 파일(**)을 포함

jobs:
  cache:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4 # checkout Action
      - name: setup-node
        uses: actions/setup-node@v3 # setup-node Action
        with:
          node-version: 18
      - name: Cache Node.js modules
        uses: actions/cache@v3 # cache Action
        with: # with 키워드를 사용 : path, key, restore-keys Input값 전달
          path: ~/.npm # 캐싱할 경로로, 그 값은 Node.js 모듈이 지정되는 .npm/ 지정
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }} # 캐시의 키 : 운영체제와 package.lock.json 값을 구성 (운영체제, 의존성 별로 캐시 구분 위함
          # runners-os : Github Actions을 실행하는 운영체제를 나타내고, 각 운영체제별로 캐시를 분리하기 위해 사용
          # node : 임의의 문자열이며, 캐시의 범주를 나타내기 위해 사용 (node.js 모듈 캐싱)
          # hashFiles : package-lock.json의 해시값을 나타내며, 이 해시값은 이 파일이 변경될 때마다 변경되므로, 의존성 추가 및 기존 의존성 변경마다 새로운 캐시를 생성하기 위해 사용
          restore-keys:
            | # 캐시 복구를 위한 키 : runner-os node를 지정하였는데, 이는 키가 정확히 일치하는 캐시가 없을 경우, 가장 가까운 캐시를 찾아 사용하기 위함
            ${{ runner.os }}-node-
          # cache Action을 설정하면, 이후 같은 운영체제에서 동일한 package.json을 가진 Workflow가 실행될 떄, 이전에 저장했던 캐시를 사용해 의존성 설치를 빠르게 수행 가능
      - name: Install dependencies # my-app 디렉토리에 필요한 의존성을 설치
        run: |
          cd my-app
          npm ci
      - name: npm build # my-app 디렉토리에서 빌드 작업 수행 (처음 실행하면 캐싱되지 않아, 캐시가 없다는 메세지 출력 / 이후 cache 액션을 처음 사용하고 완료되면, cache가 저장되었다는 메세지 확인 가능)
        run: |
          cd my-app
          npm run build
```
   - /my-app/src/App.js 수정
<div align="center">
<img src="https://github.com/user-attachments/assets/29944192-db35-45fa-861b-2649a4db9245">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/416c0bc7-ccd3-407d-83ea-2c45e5876f60">
</div>

   - /my-app/src/App.js 수정
<div align="center">
<img src="https://github.com/user-attachments/assets/47e2dfb7-9a08-4005-82cd-84cd76ca5cdc">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/5a0c58fc-6a4c-4fec-b64d-de92684cda5d">
</div>

4. Actions 탭에서 Management - Caches 탭을 확인하면, 캐시 확인 및 관리 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/1ec7e8e3-eff4-4092-a6d6-b0d05463fb22">
</div>
