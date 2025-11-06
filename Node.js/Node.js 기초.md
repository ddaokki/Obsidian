# Node.js
Node.js는 **JavaScript 실행 환경**이다. 보통 JavaScript는 브라우저에서만 실행되지만, **Node.js**는 JavaScript를 **서버**에서 실행할 수 있게 해주는 **런타임 환경**이다. **Chrome V8 엔진**을 기반으로 하여 **비동기 이벤트 기반** 방식으로 서버를 개발할 수 있게 해준다.

### 특징:
- **서버 사이드 JavaScript**: 서버에서 JavaScript를 실행할 수 있게 해준다.
- **비동기 이벤트 기반**: 빠르고 효율적인 비동기 I/O 작업을 처리할 수 있다.
- **패키지 관리**: npm(Node Package Manager)을 통해 다양한 라이브러리와 패키지를 쉽게 설치하고 사용할 수 있다.

---

### Express
**Express**는 Node.js를 기반으로 한 **웹 애플리케이션 프레임워크**이다. 쉽게 말해, Node.js로 웹 서버를 개발할 때 **더 쉽게** 만들 수 있게 도와주는 **라이브러리**이다.

### 특징:
- 웹 서버를 **쉽게 구축**할 수 있도록 도와준다.
- **라우팅**과 **미들웨어** 기능 등을 처리할 수 있게 도와준다.
- HTTP 요청과 응답을 쉽게 처리할 수 있는 기능들을 제공한다.

---

### app.js 예시
```javascript
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';
import morgan from 'morgan';

dotenv.config();
const app = express();
app.use(cors());
app.use(morgan('dev'));
app.use(express.json());

app.get('/api/health', (req, res) => {
  res.status(200).json({ status: 'OK', time: Date.now() });
});

const PORT = process.env.PORT || 4000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});

export default app;
```
#### **CORS**
**CORS**(Cross-Origin Resource Sharing)는 서버와 클라이언트가 다른 출처에서 요청을 보낼 때 발생할 수 있는 보안 문제를 해결해주는 메커니즘이다. 예를 들어, **localhost:5173**에서 실행 중인 프론트엔드 앱이 **localhost:4000**에서 실행 중인 백엔드 API에 접근할 때, CORS 설정이 필요하다.

#### **dotenv**
**dotenv**는 **환경 변수**를 쉽게 관리할 수 있도록 돕는 패키지이다. **환경 변수**는 개발, 테스트, 프로덕션 환경에서 **서버 설정값**을 다르게 설정할 때 유용하다. 예를 들어, **데이터베이스 연결 문자열**, **API 키**, **포트 번호** 등을 **`.env` 파일**에 저장하여 코드에 하드코딩하지 않고 **보안**을 유지하면서 사용할 수 있다.

#### **morgan**
**morgan**은 **HTTP 요청 로깅**을 위한 미들웨어이다. **morgan**을 사용하면, **클라이언트의 요청**(URL, HTTP 메소드, 상태 코드 등)을 **자동으로 로그로 기록**할 수 있다. 이를 통해 **서버의 요청/응답 흐름을 추적**하고, **디버깅**하거나 **서버 성능**을 모니터링하는 데 유용하다.


#### 1. `dotenv.config();`
- **의미**: **`dotenv`** 패키지를 사용하여 `.env` 파일에 저장된 환경 변수를 **프로세스 환경에 로드**하는 코드이다.
- **기능**: `.env` 파일에서 정의된 변수들을 `process.env`로 접근할 수 있게 해준다.
    - 예시: `.env` 파일에 `PORT=4000`이 설정되어 있으면, `process.env.PORT`로 이 값을 가져올 수 있다.
#### 2. `const app = express();`
- **의미**: **`express()`를 호출하여 Express 애플리케이션 인스턴스를 생성하는 코드이다.
- **기능**: Express는 **웹 서버**를 만드는 데 사용되는 프레임워크이다. 이 코드는 서버를 만들기 위한 기본 설정을 시작하는 부분이다.
#### 3. `app.use(cors());`
- **의미**: **CORS(Cross-Origin Resource Sharing)** 미들웨어를 사용하는 코드이다.
- **기능**: 다른 출처(예: 다른 포트나 도메인)에서 오는 요청을 허용하도록 설정하는 것이다.
    
    - 예시: **localhost:5173**에서 **localhost:4000**의 API를 호출하려면, **CORS** 설정이 필요하다.
        
    - **`app.use(cors())`**는 모든 도메인에서 오는 요청을 허용하는 기본 설정이다.
        

#### 4. `app.use(morgan('dev'));`
- **의미**: **`morgan`** 미들웨어를 사용하여 HTTP 요청 로그를 기록하는 코드이다.
- **기능**: 이 코드는 **`dev` 형식**의 로그를 출력한다. `dev`는 요청에 대한 간단한 로그를 콘솔에 출력해준다. 예를 들어:
    - 요청 URL, HTTP 메서드, 상태 코드 등이 출력된다.
`GET /api/health 200 13ms - 70b`

#### 5. `app.use(express.json());`
- **의미**: **`express.json()`** 미들웨어를 사용하여 **JSON 형식의 요청 본문(body)**를 자동으로 파싱하는 코드이다.
- **기능**: **POST**나 **PUT** 요청으로 전송된 **JSON 데이터를** `req.body`로 쉽게 접근할 수 있도록 만들어준다.
    - 예시: 프론트엔드에서 JSON 데이터를 보내면, 이를 자동으로 파싱해서 `req.body`로 받아서 사용할 수 있다.

#### 6. `app.get('/api/health', (req, res) => { res.status(200).json({ status: 'OK', time: Date.now() }); });`
- **의미**: **`GET` 요청**을 처리하는 라우팅 설정이다.
- **기능**: 클라이언트가 **`/api/health`** 경로로 GET 요청을 보내면, 응답으로 **JSON** 데이터를 반환한다.
    - `res.status(200)`는 **HTTP 상태 코드**를 200(성공)으로 설정한다.
    - `res.json({ status: 'OK', time: Date.now() })`는 **JSON 형식의 응답**을 반환한다. `status: 'OK'`와 `time`은 **현재 시간**을 **Date.now()**로 전달한다.

#### 7. `const PORT = process.env.PORT || 4000;
- **의미**: 서버가 실행될 **포트 번호**를 설정하는 코드이다.
- **기능**: `process.env.PORT`는 **환경 변수**에서 `PORT` 값을 가져옵니다. 만약 **`process.env.PORT`**가 정의되지 않았다면, 기본값으로 `4000`번 포트를 사용한다.
    - 환경 변수로 포트를 설정할 수 있어 **프로덕션 환경**에서 포트 번호를 외부에서 쉽게 변경할 수 있다.
        

#### 8. `app.listen(PORT, () => { console.log(`Server running on port ${PORT}`); });`
- **의미**: **Express 서버를 시작**하는 코드이다.
- **기능**: `PORT` 번호에서 서버가 **대기 상태**로 들어갑니다. 서버가 정상적으로 실행되면 **`Server running on port ${PORT}`** 메시지가 콘솔에 출력된다.
    - 이 코드로 Express 서버가 **`http://localhost:4000`**(혹은 설정한 `PORT`)에서 요청을 받을 준비를 한다.        
    
#### 9. `export default app;`
- **의미**: `app` 인스턴스를 **모듈로 내보내는 코드** 
- **기능**: 이 코드는 **`app.js`** 파일에서 **`app` 인스턴스를 외부 모듈로 export**하여 다른 파일에서 사용할 수 있도록 한다. 예를 들어, 다른 파일에서 `import app from './app';`을 통해 이 `app`을 사용할 수 있다.
    - 주로 **테스트**나 **서버 구성**을 분리할 때 유용