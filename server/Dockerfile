#### Stage 1: Build the application
FROM openjdk:11-jdk-slim as build
 
# Set the current working directory inside the image
WORKDIR /app
 
# Copy maven executable to the image
COPY mvnw .
COPY .mvn .mvn
 
# Copy the pom.xml file
COPY pom.xml .

# Copy the project source
COPY src src
 
# Build all the dependencies in preparation to go offline. 
# This is a separate step so the dependencies will be cached unless 
# the pom.xml file has changed.
RUN sh ./mvnw dependency:go-offline -B
 
# Package the application
RUN sh ./mvnw package -DskipTests
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)
 
#### Stage 2: A minimal docker image with command to run the app 
FROM openjdk:11-jre-slim
 
ARG DEPENDENCY=/app/target/dependency
VOLUME /storage
 
# Copy project dependencies from the build stage
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app
 
ENTRYPOINT ["java","-cp","app:app/lib/*","br.hotay.filemanage.FilemanageApplication"]