## global로 설치된 모듈 확인 및 삭제

```
// global로 설치된 모듈들 확인
npm list -g --depth=0

/Users/wonseokjang/.nvm/versions/node/v18.8.0/lib
├── @aws-amplify/cli@10.5.1
├── @sanity/cli@2.34.0
├── corepack@0.13.0
├── json-server@0.17.1
├── npm@8.18.0
├── serve@14.0.1
├── typescript@4.8.4
└── yarn@1.22.19
```

```
npm uninstall -g <모듈이름>
```
