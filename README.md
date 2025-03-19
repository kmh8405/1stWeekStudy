# README

# Javascript

## Callback

코드를 통해 명시적으로 호출하는 함수가 아니라 함수를 등록해놓은 후 어떤 이벤트가 발생했더나 특정 시점에 도달 했을 때 시스템에서 호출하는 함수를 말함.

- 사용 이유:
    - JS에서 비동기적 프로그래밍을 할 수 있기 때문
- 사용 유형:
    1. 익명 함수 사용
    2. 함수의 이름 넘기기
    3. 전역변수, 지역변수를 콜백함수의 파라미터로 전달가능
- 주의점:
    - this를 사용한 콜백함수의 콜백 지옥
- 예제 코드:
    
    ```jsx
    console.log('one');
    setTimeout(function() {
        console.log('two');
    }, 1000); // 1초 이후 실행
    console.log('three');
    
    //동기식으로 실행되었다면 one > 1초 기다리고 > two > three 출력
    //node에서 실행한다면 one > three > 1초 기다리고 > two 출력
    ```
    

## Promise

단어 뜻 그대로 “약속”을 의미하는데, 비동기적으로 실행되는 코드 흐름에서 특정 이벤트(성공, 실패 등)에 따라 그 결과를 전달하겠다는 약속을 의미

JS 비동기처리에 사용되는 객체

- Promise 객체에 대해
    - 비동기로 처리하는 시점을 명확히 할 수 있다
    - Promise는 객체이기 때문에 Promise “생성자 함수”를 통해 “인스턴스”가 만들어지고, **생성자 함수 내에 전달 되는 함수를 실행자(executor)**라 한다.
    - Promise를 생성할 때, 비동기적 작업이 “성공” 혹은 “실패” 했으냐에 따라 다른 결과를 전달할 수 있게 하는 “매개변수”로 resolve() 와 reject() 라는 callback 함수를 가짐
    - Promise 생성의 예제
        
        ```jsx
        let a = 1;
        console.log("프로그램 코드 시작");
        const myPromise = new Promise((resolve, reject) => {
            console.log("프로미스 생성!");
            if (a === 1) resolve(a);
            else reject(new Error("a가 1이 아닙니다!"));
        });
        myPromise.then(value => {
            console.log(value);
        });
        console.log("프로그램 코드 끝");
        
        //출력 순서:
        //프로그램 코드 시작
        //프로미스 생성!
        //프로그램 코드 끝
        //1
        ```
        
    - Promise의 3가지 상태:
        - Pending(대기): 비동기 처리 로직이 아직 완료되지 않은 상태
        - Fulfilled(이행): 비동기 처리가 완료되어 프로미스가 결과값을 반환해 준 상태
        - Rejected(실패): 비동기 처리가 실패하거나 오류가 발생한 상태
- Promise chaining
    - 하나의 promise instance로 promise가 제공하는 메서드(then(), catch(), finally())를 활용해 method chaning을 할 수 있다.
    - promise.then을 호출하면 promise가 반환되고, 따라서 result가 전달되고 .then을 또 호출 할 수 있다.
    - 예제 코드:
        
        ```jsx
        console.log("코드 시작");
        let a = 1;
        const myPromise = new Promise((resolve, reject) => {
            // 여기에 비동기 함수를 실행하면 된다.
            setTimeout(() => {
                if (a === 1) {
                    a += 1;
                    // resolve는 fulfilled 된 상태
                    resolve(a);
                }
                // reject는 rejected 된 상태
                else reject(new Error("a는 1이 아닙니다."));
            }, 2000);
        });
        
        myPromise
            .then(value => {
                console.log(value);
                return value;
            })
            .then(value => {
                console.log(value * value);
                return value * value;
            })
            .then(value => {
                console.log(value * value);
                return value * value;
            })
            .then(value => {
                console.log(value * value);
            })
            .catch(err => {
                console.error(err);
            })
            // 성공 실패 모두 실행하는 finally
            .finally(() => {
                console.log("모든 작업 끝!");
            })
            
        //출력 순서:
        	//코드 시작
        	//코드 끝
        	//2
        	//4
        	//16
        	//256
        	//모든 작업 끝!
        ```
        
- Promise Error Handling
    - Promise가 거부되면 제어 흐름이 제일 가까운 rejection 핸들러로 넘어가기 때문에 Promise chain을 사용하면 에러를 쉽게 처리할 수 있다.
    - 가장 쉬운 에러 처리 방법은 체인 끝에 .catch를 붙이는 것
        - 정상적인 경우라면 .catch는 절대 트리거 되지 않지만, 네트워크 문제, 잘못된 형식의 JSON 등으로 인해 위쪽 promise 중 하나라도 거부되면 .catch에서 에러를 잡게 된다.
    - 암시적 try…catch
        - executor 과 핸들러 코드 주위엔 ‘보이지 않는(암시적) try…catch’가 있다
        - 예외가 발생하면 암시적 try…catch 에서 예외를 잡고 이를 reject 처럼 다룬다
        - executor 주위의 ‘암시적 try…catch’는 스스로 에러를 잡고 에러를 거부상태의 promise로 변경시킨다.
            - 예제 코드:
                
                ```jsx
                new Promise((resolve, reject) => {
                  resolve("OK");
                }).then((result) => {
                  throw new Error("에러 발생!"); // 프라미스가 거부됨
                }).catch(alert); // Error: 에러 발생!
                
                //---------------------------------------------------------//
                
                new Promise((resolve, reject) => {
                  resolve("OK");
                }).then((result) => {
                  blabla(); // 존재하지 않는 함수
                }).catch(alert); // ReferenceError: blabla is not defined
                ```
                
    - 다시 던지기
        - catch에서 조건 외의 에러 발생 시 error를 던지고 마지막에 또 에러 처리
        - 일반 try…catch 에선 에러를 분석하고, 처리할 수 없는 에러라 판단되면 에러를 다시 던질 때가 있는데, promise에도 유사한 일을 할 수 있다.
        - 아래 예시에서 (*)로 표시한 핸들러에서 에러를 잡는데, 여기서는 에러를 처리하지 못하기 때문에 (URIError 처리 방법만 알고 있음) 에러를 다시 던진다.
            
            ```jsx
            // 실행 순서: catch -> catch
            new Promise((resolve, reject) => {
            
              throw new Error("에러 발생!");
            
            }).catch(function(error) { // (*)
            
              if (error instanceof URIError) {
                // 에러 처리
              } else {
                alert("처리할 수 없는 에러");
            
                throw error; // 에러 다시 던지기
              }
            
            }).then(function() {
              /* 여기는 실행되지 않습니다. */
            }).catch(error => { // (**)
            
              alert(`알 수 없는 에러가 발생함: ${error}`);
              // 반환값이 없음 => 실행이 계속됨
            
            });
            ```
            
            - 실행 흐름이 첫 번째 .catch () 로 넘어갔다가 다음 .catch (**)로 이어진다.
    - 처리되지 못한 거부
        - 에러를 처리하지 못 했을 때, promise는 거부 상태가 되고 실행 흐름은 가장 가까운 rejection 핸들러로 넘어가는데 위 예시에는 예외를 처리해 줄 핸들러가 없어서 에러가 ‘갇혀버린다’. → 에러를 처리할 코드가 없기 때문
        - 자바스크립트 엔진은 promise 거부를 추적하다가 위와 같은 상황이 발생하면 전역 에러를 생성
        - 브라우저 환경에서는 이런 에러를 unhandledrejection 이벤트로 처리 가능
            
            ```jsx
            window.addEventListener('unhandledrejection', function(event) {
              // unhandledrejection 이벤트엔 두 개의 특수 프로퍼티가 있습니다.
              alert(event.promise); // [object Promise] - 에러를 생성하는 프라미스
              alert(event.reason); // Error: 에러 발생! - 처리하지 못한 에러 객체
            });
            
            new Promise(function() {
              throw new Error("에러 발생!");
            }); // 에러를 처리할 수 있는 .catch 핸들러가 없음
            ```
            
    - 예제 코드:
        
        ```jsx
        const getHen = () =>
          new Promise((resolve, reject) => {
            setTimeout(() => resolve('chicken'), 1000);
          });
        const getEgg = hen =>
          new Promise((resolve, reject) => {
            setTimeout(() => reject(new Error(`error! ${hen} => egg`)), 1000);
          });
        const cook = egg =>
          new Promise((resolve, reject) => {
            setTimeout(() => resolve(`${egg} => sunnyside`), 1000);
          });
        
          getHen()
            .then(getEgg)
            .catch(error => {
              return 'bread';
            })
            .then(cook)
            .then(console.log)
            .catch(console.log);
        ```
        

## Async & await

- async:
    - async는 function 앞에 위치
    - async가 붙은 함수는 반드시 promise를 반환
    - promise가 아닌 것은 promise로 감싸 반환한다.
    - async 함수는 화살표 함수, 함수 표현식으로도 정의 가능
- await:
    - async 함수 안에서만 동작
    - promise가 처리될 때 까지 기다리고, 결과는 그 이후 반환
- async & await 는 비동기 코드도 동기 코드처럼 읽히는 효과를 얻을 수 있다는 점이 장점.
- 예제 코드:
    
    ```jsx
    function delay(ms){
    	return new Promise(resolve => setTimeout(resolve, ms));
    }
    
    async function getApple(){
    	await delay(2000)
      return "Apple"
    }
    
    async function getBanana(){
    	await delay(1000)
      return "Banana"
    }
    
    getApple().then((data) => console.log(data)) //'Apple'
    getBanana().then((data) => console.log(data)) //'Banana'
    ```
    

# Node.js

## 파일 시스템

Node.js의 파일 시스템 모듈은 파일의 생성, 삭제, 읽기, 쓰기 등을 수행할 수 있다.

이 File System 모듈은 내장 모듈로써 따로 설치 할 필요는 없으며, 

```jsx
const fs = require("fs");
```

를 사용해 주면 모듈을 불러올 수 있다.

require 함수 사용 이외에 fs 모듈을 불러오기 위한 또 다른 방법이 존재하는데

```jsx
import fs from "fs";
```

를 사용하는 것이다.

파일 기능 함수로는 크게 4가지가 있고, 동기/비동기에 따라 사용되는 함수 코드도 약간 다르다.

- 파일 읽기 - readFile
    - 동기적 읽기 → fs.readFileSync(file path, [options]) 형태로 작성
    - 비동기적 읽기 → fs.readFile(file path, [options], callback) 형태로 작성
    - 예제 코드)
        
        ```jsx
        const fs = require("fs");
        
        //비동기
        fs.readFile('./test.txt', 'utf8', (err, data) => {
        	console.log(data+"(1)");
        });
        
        //동기
        var data = fs.readFileSync('./test.txt', "utf8");
        console.log(data+"(2)");
        ```
        
        - 만약 아무런 변환 없이 그대로 출력한다면 버퍼(Buffer) 형태로 출력되는데, 문자로 된 실제 파일 데이터를 읽고 싶다면 toString() 메서드로 따로 변환 해 주어야 한다.
        
        ```jsx
        console.log(data);
        ->
        console.log(data.toString());
        ```
        
- 파일 쓰기 - writeFile
    - 동기적 쓰기 → fs.writeFileSync(경로, data, [options])
    - 비동기적 쓰기 → fs.writeFile(경로, data, [options], callback)
    - 예제 코드)
        
        ```jsx
        const fs = require("fs");
        
        //비동기
        fs.writeFile('./test.txt', "수정합니다.", (err) => {
        	fs.readFile('./test.txt', 'utf8', (err, data) => {
        		console.log(data);
        	});
        })
        
        //동기
        fs.writeFileSync('./test.txt', "수정합니다.");
        var data = fs.readFileSync('./test.txt', "utf8");
        console.log(data);
        ```
        
        - 만약 기존 내용이 있는 파일에 writeFile을 해버린다면 아예 덮어 씌워지기 때문에 기존 내용 뒤에 덧붙이려면 appendFile() 메서드를 사용해야 한다.
- 파일 이름 수정 - rename
    
    동기 → fs.renameSync(oldPath, newPath)
    
    비동기 → fs.rename(oldPath, newPath, callback)
    
- 파일 삭제 - unlink
    
    동기 → fs.unlinkSync(path)
    
    비동기 → fs.unlink(Path, callback)
    
- 여기서 알 수 있듯이, Sync()가 붙는다면 동기, 아니면 비동기 함수이다.

이외에도 Promises 를 사용하는 기능도 있는데, fs 모듈은 기본적으로 콜백 형태로 사용하기 때문에 복잡한 로직이 들어가게 된다면 사용하기가 불편하다. promises 속성을 통해 프로미스로 감싸지 않고 then 문법을 사용할 수 있다.

- 예제 코드)
    
    ```jsx
    const fs = require('fs').promises;
    
    fs.readFile('./resource/README.md')
    	.then((data) => {
    		return data.toString();
    	})
    	.then((fileData) => {
    		console.log(fileData);
    	})
    	.catch((error) => {
    		console.error(error);
    	});
    ```
    
- 

## Buffer & Stream

- Buffer
    - 개념:
        - 고정 크기의 메모리 공간을 사용하여 이진 데이터를 저장하는 객체
    - 특징:
        - 고정된 크기를 갖고, 초기화 시 크기를 정해야 함
        - Raw binary data(원시 이진 데이터) 처리가 가능하며, UTF-8, Base64, Hex 등의 인코딩 지원
    - 주요 메서드:
        - Buffer.from() : 문자열, 배열 등으로부터 Buffer 객체 생성
        - Buffer.toString() : Buffer 객체를 다시 문자열로 변환
        - Buffer.length : Buffer의 길이를 바이트 단위로 반환
    - 예시 코드:
        
        ```jsx
        // Buffer 생성: 'Hello' 문자열을 Buffer로 변환
        const buf = Buffer.from('Hello', 'utf8');
        
        console.log(buf);               // <Buffer 48 65 6c 6c 6f> (16진수로 출력)
        console.log(buf.toString());    // Hello (UTF-8로 다시 문자열로 변환)
        
        // Buffer의 길이 출력
        console.log(buf.length);        // 5 (바이트 단위)
        ```
        
- Stream
    - 개념:
        - 연속적인 데이터 흐름을 처리하는 방식
        - 대용량 데이터를 작은 청크(chunk) 단위로 나누어 처리가 가능 → 메모리를 효율적으로 사용하면서 데이터 전송 및 처리가 가능
    - 장점:
        - 메모리 사용량이 적음
        - 데이터를 스트리밍 방식으로 처리하면서 읽고 쓰는 작업을 동시에 할 수 있음
    - 주요 메소드:
        - stream.on(’data’, callback)
        - stream.on(’end’, callback)
        - stream.on(’error’, callback)
        - stream.on(’drain’, callback)
        - stream.write(data, [encoding], callback)
        - stream.end([data], [encoding], callback)
    - 4가지 유형:
        1. Writable Stream: 쓰기 작업을 지원하는 스트림
            - 예시 코드
                
                ```jsx
                const fs = require('fs');
                
                // 파일을 쓰기 위한 Writable Stream 생성
                const writableStream = fs.createWriteStream('output.txt');
                
                writableStream.write('첫 번째 라인\n', 'utf8');
                writableStream.write('두 번째 라인\n', 'utf8');
                
                // 스트림 끝내기
                writableStream.end();
                
                writableStream.on('finish', () => {
                    console.log('파일 쓰기 완료');
                });
                
                writableStream.on('error', (err) => {
                    console.error('파일 쓰기 중 오류 발생:', err);
                });
                ```
                
        2. Readable Stream: 읽기 작업을 지원하는 스트림
            - 예시 코드
                
                ```jsx
                const fs = require('fs');
                
                const data = [];
                // 파일을 읽기 위한 Readable Stream 생성
                const readableStream = fs.createReadStream('output.txt', {
                    highWaterMark: 16  // 버퍼 크기 (청크 단위) 설정, 16바이트
                });
                
                readableStream.on('data', (chunk) => {
                    console.log('새로운 청크 수신:', chunk, chunk.length);
                		data.push(chunk);
                });
                
                readableStream.on('end', () => {
                    console.log('파일 읽기 완료');
                		console.log(Buffer.concat(data).toString());
                });
                
                readableStream.on('error', (err) => {
                    console.error('파일 읽기 중 오류 발생:', err);
                });
                ```
                
        3. Duplex Stream: 읽기와 쓰기 모두 가능한 스트림
        4. Transform Stream: 읽은 데이터를 변환하고, 변환된 데이터를 쓰는 스크림

## 기타 fs 메소드

- 파일의 유무 확인 (fs.stat())
    - 예시)
        
        ```jsx
        fs.stat("Information.json", (err, stats) => {
        	if (err) console.log(err)
        	else console.log('파일 찾음')
        })
        ```
        
        - 첫 번째 인자에 유무를 확인하고 싶은 파일명 입력(경로는 필요 없어 보임)
        - 나머지는 기존과 동일
- 파일 접근 여부 확인 (fs.access())
    - 파일에 올바르게 접근할 수 있는지 파악 가능
    - fs 모듈의 constants 속성으로 경로에 대한 권한, 읽기 및 쓰기 여부를 판단
        - F_OK: 존재 여부
        - W_OK: 쓰기 권한 여부
        - R_OK: 읽기 권한 여부
    - 예시)
        
        ```jsx
        const fs = require('fs').promises;
        const constants = require('fs').constants'
        
        fs.access('./assets', constants.F_OK | constants.W_OK | constants.R_OK)
        	.then(() => {
        		return Promise.reject('exist folder!');
        	})
        	.catch((err) => {
        		console.log(err.code);
        	});
        ```
        
- 디렉토리 생성(fs.mkdir())
    
    ```jsx
    fs.mkdir('./dir_name');
    ```
    
- 디렉토리 내용 읽어오기(fs.readdir())
    
    ```jsx
    fs.readdir('../resource').then((dir) => {
    	console.log('디렉토리 구조', dir); //디렉토리 구조 ['README.md', 'README2.md']
    });
    ```
    
- 파일/폴더의 변경 사항 감시(fs.watch())
    
    ```jsx
    const fs = require('fs');
    
    fs.watch('./target.txt', (eventType, filename) => {
        console.log(eventType, filename);
    });
    
    // 위 코드를 실행후 target.txt의 내용 또는 파일 변을 변경하면 터미널 console에 표기
    
    // 내용 변경
    // change target.txt
    // change target.txt
    // change 이벤트가 2번씩 발생 하기도 하므로 실무에서 사용할 때 주의해야한다.
    
    // 폴더 변경 및 삭제
    // rename change_target.txt
    // rename 이벤트 이후 더 이상 watch가 수행 되지않는다. 
    ```
    

## Thread Pool

Node.js에서 thread pool은 I/O 작업을 처리하는데 사용되는 스레드의 집합으로, Node.js는 기본적으로 Single thread 동작이므로 블로킹 되는 I/O 작업을 처리할 때는 비동기적으로 수행하는 것이 중요하고, 이를 위해 Node.js는 내부적으로 thread pool을 사용하여 I/O 작업을 비동기적으로 처리한다.

Node.js의 thread pool은 기본적으로 4개의 thread를 사용한다 → libuv에서 관리

> Its default size is 4, but it can be changed at startup time by setting the [UV_THEADPOOL_SIZE] environment variable to any value (the absolute maximum is 1024). - libuv docs
> 

.js 파일 내에서 값을 할당하는 것은 없고, Node.js가 실행되는 EntryPoint에서 바로 지정해줘야 함

스레드 풀의 크기는 노드의 환경 변수를 통해 조정할 수 있고 이를 통해 노드 애플리케이션의 성능을 최적화할 수 있다. (너무 크게 설정하면, context switching overhead가 증가하여 성능이 저하될 수 있으므로 적절한 크기로 설정)

- UV_THREADPOOL_SIZE 설정 방법:
    - Windows CLI
        
        ```jsx
        $ env:UV_THREADPOOL_SIZE=<value>
        $ node test.js
        ```
        
    - macOS/Linux
        
        ```jsx
        $ export UV_TREADPOOL_SIZE <value>
        $node test.js
        ```
        
- Thread Pool 용도
    - CPU 또는 I/O intensive한 작업에 내부적으로 쓰임 (개발자가 직접 컨트롤하지 않아도)
    - Node.js Event loop에서 처리하기에 무거운 작업이 자동으로 thread pool 내의 thread 에 위임된다
        - ex) ‘crypto’ 모듈의 해시함수 연산, 기타 ‘fs’ 모듈 파일 관련 작업
    - 예제 코드: (UV_THREADPOOL_SIZE 값을 바꿔가면서 실행해보자)
        
        ```jsx
        import {pbkdf2} from 'crypto'
        import * as process from 'process'
        
        console.log('worker thread number is : ' + process.env.UV_THREADPOOL_SIZE)
        
        console.time('Hashing1')
        pbkdf2('a', 'b', 100_000, 512, 'sha512', ()=>{
          console.timeEnd('Hashing1')
        })
        
        console.time('Hashing2')
        pbkdf2('a', 'b', 100_000, 512, 'sha512', ()=>{
          console.timeEnd('Hashing2')
        })
        
        console.time('Hashing3')
        pbkdf2('a', 'b', 100_000, 512, 'sha512', ()=>{
          console.timeEnd('Hashing3')
        })
        
        console.time('Hashing4')
        pbkdf2('a', 'b', 100_000, 512, 'sha512', ()=>{
          console.timeEnd('Hashing4')
        })
        
        console.time('Hashing5')
        pbkdf2('a', 'b', 100_000, 512, 'sha512', ()=>{
          console.timeEnd('Hashing5')
        })
        ```
        
        1. thread pool 기본값(4)로 5개 해시함수 실행하면
            
            ```jsx
            worker thread number is : undefined
            Hashing3: 351.294ms
            Hashing4: 361.309ms
            Hashing1: 366.053ms
            Hashing2: 371.932ms
            Hashing5: 658.325ms
            
            //기본값이 4개이기 때문에 1~4번 해싱작업은 동시에 처리되고 5번은 이후에
            //thread가 할당되어 처리한 것을 알 수 있다.
            ```
            
        2. thread pool (5)개로 해시함수 5개 실행하면
            
            ```jsx
            # 쓰레드풀 사이즈 5 지정
            $env:UV_THREADPOOL_SIZE=5
            
            worker thread number is : 5
            Hashing4: 318.785ms
            Hashing5: 328.197ms
            Hashing2: 329.408ms
            Hashing1: 347.599ms
            Hashing3: 360.894ms
            
            //thread pool 5개를 모두 사용하여 5개 해싱작업이 동시에 이루어짐.
            ```