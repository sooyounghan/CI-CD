-----
### 구축한 CI / CD 모듈화 하기 1
-----
1. github-action-module Repository 생성 후, 생성한 Repository를 git clone
2. /common/test/action.yaml (test module)
```yaml
name: test
author: SooYoung
description: "Use test job in github action"
inputs:
  NODE_VERSION:
    description: "Set node version"
    required: true
    default: "18"
  WORKING_DIRECTORY:
    description: "Set work directory"
    required: true
    default: "my-app"

runs:
  using: "composite"
  steps:
    - uses: actions/setup-node@v3
      with:
        node-version: ${{ inputs.NODE_VERSION }}
    - name: Cache Node.js modules
      uses: actions/cache@v3
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
    - name: Install dependencies
      shell: bash
      run: |
        cd ${{ inputs.WORKING_DIRECTORY }}
        npm ci
    - name: npm build
      shell: bash
      run: |
        cd ${{ inputs.WORKING_DIRECTORY }}
        npm run build
```

3. /common/set-environment/action.yaml (set-environment Module)
```yaml
name: set-environment
author: SooYoung
description: "Use set-environment module int github action"
inputs:
  REF_TYPE:
    description: "branch or tag"
    required: true
    default: "branch"
  BASE_REF:
    description: "dev or master"
    required: true
    default: "dev"
outputs:
  environment:
    description: "Get env"
    value: ${{ steps.set-env.outputs.environment }}

runs:
  using: "composite"
  steps:
    - name: set env
      id: set-env
      shell: bash
      run: |
        if [[ ${{ inputs.REF_TYPE }} == "tag" ]]; then
            echo "environment=qa" >> $GITHUB_OUTPUT
            exit 0
        fi

        if [[ ${{ inputs.REF_TYPE }} == "branch" ]]; then
            echo "environment=dev" >> $GITHUB_OUTPUT
          if [[ ${{ inputs.BASE_REF }} == "master" ]]; then
            echo "environment=staging" >> $GITHUB_OUTPUT
          fi
        fi
    - name: check output
      shell: bash
      run: |
        echo ${{ steps.set-env.outputs.environment }}
```

4. /common/aws/action.yaml
```yaml
name: aws
author: SooYoung
description: "Use aws module int github action"
inputs:
  AWS_REGION:
    description: "Set AWS REGION"
    required: true
    default: "ap-northeast-1"
  AWS_ROLE_TO_ASSUME:
    description: "Set AWS Assume Role arn"
    required: true
    default: "arn:aws:iam::123456789:role/GithubActions"

runs:
  using: "composite"
  steps:
    - name: Configure AWS Credentials
      id: credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-region: ${{ inputs.AWS_REGION }}
        role-to-assume: ${{ inputs.AWS_ROLE_TO_ASSUME }}
```

5. /common/image-build/action.yaml
```yaml
name: image build
author: SooYoung
description: "Use image build module in github action"
inputs:
  REPOSITORY:
    description: "Set aws ecr registry"
    required: true
    default: ""
  REGISTRY:
    description: "Set aws ecr repository"
    required: true
    default: ""
  DOCKERFILE_PATH:
    description: "Set aws ecr repository"
    required: false
    default: "Dockerfile"

runs:
  using: "composite"
  steps:
    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v2
      with:
        mask-password: "true"
    - name: docker build & push
      shell: bash
      run: |
        docker build -f ${{ inputs.DOCKERFILE_PATH }} --tag ${{ inputs.REGISTRY }}/${{ inputs.REPOSITORY }}:${{ github.sha }} .
        docker push ${{ inputs.REGISTRY }}/${{ inputs.REPOSITORY }}:${{ github.sha }}
```

6. /common/deploy/action.yaml
```yaml
name: deploy
author: SooYoung
description: "Use deploy module in github action"
inputs:
  KUBECTL_VERSION:
    description: "Set kubectl version"
    required: false
    default: latest
  HELM_VERSION:
    description: "Set helm version"
    required: false
    default: v3.11.1
  CLUSTER_NAME:
    description: "Set eks cluster name"
    required: true
    default: "github-actions"
  RELEASE_NAME:
    description: "Set release name"
    required: true
    default: "my-app"
  HELM_CHART_PATH:
    description: "Set helm chart path"
    required: true
    default: "helm-chart"
  NAMESPACE:
    description: "Set eks namespace"
    required: true
    default: "my-app-dev"
  REPOSITORY:
    description: "Set image repository"
    required: true
    default: ""

runs:
  using: "composite"
  steps:
    - name: setup kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: ${{ inputs.KUBECTL_VERSION }}
    - name: setup helm
      uses: azure/setup-helm@v3
      with:
        version: ${{ inputs.HELM_VERSION }}
    - name: access kubernetes
      shell: bash
      run: |
        aws eks update-kubeconfig --name ${{ inputs.CLUSTER_NAME }}
    - name: deploy
      shell: bash
      run: |
        helm upgrade --install ${{ inputs.RELEASE_NAME }} ${{ inputs.HELM_CHART_PATH }} --create-namespace --namespace ${{ inputs.NAMESPACE }} \
        --set image.tag=${{ github.sha }} \
        --set image.repository=${{ inputs.REPOSITORY }}
```

7. /common/slack/action.yaml
```yaml
name: slack
author: SooYoung
description: "Use slack module in github action"
inputs:
  DEPLOY_STEP_STATUS:
    description: "set deploy step status"
    required: true
    default: "success"
  ENVIRONMENT:
    description: "set env"
    required: true
    default: "dev"
  SLACK_WEBHOOK_URL:
    description: "set slack webhook url"
    required: true
    default: ""

runs:
  using: "composite"
  steps:
    - name: notify
      uses: slackapi/slack-github-action@v1.24.0
      with:
        payload: |
          {
            "text": "message",
            "blocks": [
              {
                "type": "section",
                "text": {
                  "type": "mrkdwn",
                  "text": "Environment : ${{ inputs.ENVIRONMENT }}, Deploy Result: ${{ inputs.DEPLOY_STEP_STATUS  }}, Repository: ${{ github.repository }}."
                }
              }
            ]
          }
      env:
        SLACK_WEBHOOK_URL: ${{ inputs.SLACK_WEBHOOK_URL }}
        SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```

8. /common/create-pr/action.yaml
```yaml
name: create-pr
author: SooYoung
description: "Use create-pr module in github action"
inputs:
  PERSONAL_ACCESS_TOKEN:
    description: "set pat"
    required: true
    default: ""
  HEAD:
    description: "set head branch"
    required: true
    default: "release/v0.0.0"
  BASE:
    description: "set base branch"
    required: true
    default: "master"
runs:
  using: "composite"
  steps:
    - name: gh auth login
      shell: bash
      run: |
        echo ${{ inputs.PERSONAL_ACCESS_TOKEN }} | gh auth login --with-token
    - name: create branch
      shell: bash
      run: |
        git checkout -b ${{ inputs.HEAD }}
        git push origin ${{ inputs.HEAD }}
    - name: Create Pull Request
      shell: bash
      run: |
        gh pr create --base ${{ inputs.BASE }} --head ${{ inputs.HEAD }} --title "${{ inputs.HEAD }} -> master" --body "release pr"
```

9. Github github-actions-module에 Push

-----
### 구축한 CI / CD 모듈화하기 2
-----
1. cicd-5.yaml
```yaml
name: cicd-5
on:
  push:
    paths:
      - "my-app/**"
    tags:
      - "v[0-9]+.[0-9]+.[0-9]+"
  pull_request:
    types: [opened, synchronize, closed]
    branches: [dev, master]
    paths:
      - "my-app/**"

jobs:
  test: # CI
    if: github.event.action == 'opened' || github.event.action == 'synchronize'
    runs-on: ubuntu-latest
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: check out module code
        uses: actions/checkout@v4
        with:
          repository: "sooyoung-action/github-actions-module"
          path: ./actions-module
      - name: use test module
        uses: ./actions-module/common/test
        with:
          NODE_VERSION: "18"
          WORKING_DIRECTORY: "my-app"

  set-environment:
    if: github.event.pull_request.merged == true || github.ref_type == 'tag'
    runs-on: ubuntu-latest
    outputs:
      environment: ${{ steps.set-env.outputs.environment }}
    steps:
      - name: check the module code
        uses: actions/checkout@v4
        with:
          repository: "sooyoung-action/github-actions-module"
          path: ./actions-module
      - name: use set-environment module
        uses: ./actions-module/common/set-environment
        id: set-env
        with:
          REF_TYPE: ${{ github.ref_type }}
          BASE_REF: ${{ github.base_ref }}

  image-build:
    needs: [set-environment]
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    strategy:
      matrix:
        environment: ["${{ needs.set-environment.outputs.environment }}"]
    environment: ${{ matrix.environment }}
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: checkout the module code
        uses: actions/checkout@v4
        with:
          repository: "sooyoung-action/github-actions-module"
          path: ./actions-module
      - name: use aws module
        uses: ./actions-module/common/aws
        with:
          AWS_REGION: ${{ vars.AWS_REGION }}
          AWS_ROLE_TO_ASSUME: ${{ secrets.AWS_ROLE_TO_ASSUME }}
      - name: use image-build module
        uses: ./actions-module/common/image-build
        with:
          REPOSITORY: ${{ vars.REPOSITORY }}
          REGISTRY: ${{ secrets.REGISTRY }}

  deploy: # CD
    runs-on: ubuntu-latest
    needs: [set-environment, image-build]
    permissions:
      id-token: write
      contents: read
    strategy:
      matrix:
        environment: ["${{ needs.set-environment.outputs.environment }}"]
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: checkout the module code
        uses: actions/checkout@v4
        with:
          repository: "sooyoung-action/github-actions-module"
          path: ./actions-module
      - name: use aws module
        uses: ./actions-module/common/aws
        with:
          AWS_REGION: ${{ vars.AWS_REGION }}
          AWS_ROLE_TO_ASSUME: ${{ secrets.AWS_ROLE_TO_ASSUME }}
      - name: use deploy module
        id: status
        uses: ./actions-module/common/deploy
        with:
          CLUSTER_NAME: ${{ vars.CLUSTER_NAME }}
          RELEASE_NAME: my-app
          HELM_CHART_PATH: kubernetes/my-app
          NAMESPACE: my-app-${{ matrix.environment }}
          REPOSITORY: ${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}
      - name: use slack module
        if: always()
        uses: ./actions-module/common/slack
        with:
          DEPLOY_STEP_STATUS: ${{ steps.status.outcome }}
          ENVIRONMENT: ${{ matrix.environment }}
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

  create-pr:
    if: needs.set-environment.outputs.environment == 'qa'
    runs-on: ubuntu-latest
    needs: [set-environment, deploy]
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: checkout the module code
        uses: actions/checkout@v4
        with:
          repository: "sooyoung-action/github-actions-module"
          path: ./actions-module
      - name: use create-pr module
        uses: ./actions-module/common/create-pr
        with:
          PERSONAL_ACCESS_TOKEN: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
          HEAD: release/${{ github.ref_name }}
          BASE: master

  approve:
    if: needs.set-environment.outputs.environment == 'staging'
    runs-on: ubuntu-latest
    environment: approve-process
    needs: [set-environment, deploy]
    steps:
      - name: approve
        run: |
          echo "Approve Done"

  prod-deploy:
    runs-on: ubuntu-latest
    needs: [approve]
    permissions:
      id-token: write
      contents: read
    strategy:
      matrix:
        environment: ["prod"]
    environment: ${{ matrix.environment }}
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: checkout the module code
        uses: actions/checkout@v4
        with:
          repository: "sooyoung-action/github-actions-module"
          path: ./actions-module
      - name: use aws module
        uses: ./actions-module/common/aws
        with:
          AWS_REGION: ${{ vars.AWS_REGION }}
          AWS_ROLE_TO_ASSUME: ${{ secrets.AWS_ROLE_TO_ASSUME }}
      - name: use deploy module
        id: status
        uses: ./actions-module/common/deploy
        with:
          CLUSTER_NAME: ${{ vars.CLUSTER_NAME }}
          RELEASE_NAME: my-app
          HELM_CHART_PATH: kubernetes/my-app
          NAMESPACE: my-app-${{ matrix.environment }}
          REPOSITORY: ${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}
      - name: use slack module
        if: always()
        uses: ./actions-module/common/slack
        with:
          DEPLOY_STEP_STATUS: ${{ steps.status.outcome }}
          ENVIRONMENT: ${{ matrix.environment }}
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```
