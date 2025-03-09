# Gradle로 스프링 실행시키기

Gradle에 JVM강제 설정해서 돌리기 Zulu 21 사용하려고(인텔리제이를 무한하게 돌릴 수 없기 때문에)
특정 디렉터리 이동해서
```
gradlew bootRun -Dorg.gradle.java.home="C:/Program Files/Zulu/zulu-21"
```


특정포트 확인
```
netstat -ano | find ""
```

프로세스 종료
```
taskkill /PID 12345 /F
```











#window_포트확인 #window_프로세스종료 [[스프링]] [[Gralde]]