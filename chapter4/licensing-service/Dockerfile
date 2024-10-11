#stage 1
#Start with a base image containing Java runtime
#도커 런타임에 사용될 도커 이미지를 지정
FROM openjdk:11-slim as build

# Add Maintainer Info
LABEL maintainer="Illary Huaylupo <illaryhs@gmail.com>"

# The application's jar file
# dockerfile-maven-plugin에 설정된 JAR_FILE 변수를정의
ARG JAR_FILE

# Add the application's jar to the container
# JAR 파일을 이미지의 파일시스템에 app.jar로복사
COPY ${JAR_FILE} app.jar

#unpackage jar file
#앞서 빌드 이미지의 파일 시스템에 복사한 app.jar의 압축을 푼다
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf /app.jar)

#stage 2
#Same Java runtime
FROM openjdk:11-slim

#Add volume pointing to /tmp
VOLUME /tmp

#Copy unpackaged application to new container
# stage 1에서 build라고 명명된 첫 이미지에서 여러 레이어를 복사한다.
ARG DEPENDENCY=/target/dependency
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

#execute the application
# 컨테이너가 생성될 때 애플리케이션을 실행 대상으로 지정
ENTRYPOINT ["java","-cp","app:app/lib/*","com.optimagrowth.license.LicenseServiceApplication"]