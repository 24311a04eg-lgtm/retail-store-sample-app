# Build Stage
FROM maven:3.9-eclipse-temurin-21 AS build-env

WORKDIR /app

COPY pom.xml ./
COPY src ./src

RUN mvn -DskipTests package -q

# Runtime Stage
FROM public.ecr.aws/amazonlinux/amazonlinux:2023

RUN dnf --setopt=install_weak_deps=False install -q -y \
    java-21-amazon-corretto-headless \
    shadow-utils \
    && dnf -q -y swap libcurl-minimal libcurl-full \
    && dnf -q -y swap curl-minimal curl-full \
    && dnf clean all \
    && useradd \
       --home /app \
       --create-home \
       --user-group \
       --uid 1000 \
       appuser

WORKDIR /app

COPY --from=build-env /app/target/ui-0.0.1-SNAPSHOT.jar ./app.jar

RUN chown -R appuser:appuser /app

USER appuser

ENV SPRING_PROFILES_ACTIVE=prod
ENV JAVA_TOOL_OPTIONS=""

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]
