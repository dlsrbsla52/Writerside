# Nginx Temp 디렉토리 권한 변경에 다른 장애 케이스


### 1. Nginx의 임시 디렉터리 사용 방식

- **요청 본문(request body) 버퍼링**  
    Nginx는 클라이언트로부터 전송된 요청 본문을 메모리에서 처리하기에 너무 클 경우, 설정된 임시 디렉터리(예, `client_body_temp_path`)에 저장.
    
- **업스트림(예, proxy, fastcgi 등) 응답 버퍼링**  
    업스트림 서버(여기서는 Spring Boot)가 반환하는 응답이 메모리 버퍼(예, `proxy_buffer_size`, `fastcgi_buffers` 등)의 한계를 넘으면, Nginx는 설정된 임시 경로(예, `proxy_temp_path` 또는 `fastcgi_temp_path`)에 데이터를 저장.
    
즉, 응답 데이터가 크거나 처리 시간이 길어져 메모리 버퍼에 모두 담지 못할 경우, Nginx는 **임시 파일**로 데이터를 디스크에 기록하여 응답을 완성.


### 2. 디렉터리(소유자 권한 변경)

- **스크립트를 통한 디렉터리의 소유권 변경**
  Nginx 취약점 스크립트를 실행하면서 Temp쪽 디렉터리의 소유권에 대한 변경이 일어남 (etoosadm -> nobody)
  
- **디렉터리 소유권 변경에 따른 접근 제한**
  was가 데이터를 처리하고 있는 와중 Nginx는 데이터를 temp에 담기 위해 Temp 디렉토리에 접근 **여기서 Temp 디렉토리에 접근할 수 없는 문제 발생**

- **Nginx가 디렉터리에 접근할 수 없음**
  Nginx가 디렉터리에 접근할 수 없으므로 Nginx는 클라이언트에 대한 response 즉 응답을 멈춰버림.
  이를 통해 was는 broken pip라는 에러 메세지를 던지고 처리를 종료함

### 3. 해결방안

- Nginx가 데이터를 처리할 수 있도록 Temp의 권한을 기존 etoosadm으로 변경

