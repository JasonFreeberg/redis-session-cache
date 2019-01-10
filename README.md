# Ready 2019: Spring & Azure Redis on App Service

> This is a stripped-down version of the original [Spring example project](https://github.com/spring-projects/spring-session/archive/2.1.2.RELEASE.zip).

Spring Session consists of the following modules:

- [Spring Session Core](/spring-session-core/src/main/java/org/springframework/session) - provides core Spring Session functionalities and APIs
- [Spring Session Data Redis](/spring-session-data-redis/src/main/java/org/springframework/session/data/redis) - provides SessionRepository and ReactiveSessionRepository implementation backed by Redis and configuration support

## Configuration Notes

1. Add the connection parameters to `application.properties`

   Add the following to the file:

   ```
   spring.session.store-type=redis
   spring.redis.host=  # Your hostname from portal
   spring.redis.password=  # Primary key from portal
   spring.redis.port=6379
   ```

   The bit, `spring.security.user.password=password`, in [application.properties](/samples/boot/redis/src/main/resources/application.properties) is the username ("user") and password ("password")to use in the HTML form.

1. Update the Java configuration

   Add this Bean to `web.http.RedisHttpSessionConfiguration` so the Redis client does not invoke the `CONFIG` command. [Documented here.](https://docs.spring.io/spring-session/docs/current/reference/html5/#api-redisoperationssessionrepository-sessiondestroyedevent)

   ```
   @Bean
   public static ConfigureRedisAction configureRedisAction() {
       return ConfigureRedisAction.NO_OP;
   }
   ```

1. Prove the session is cached

   Start the application with the following command. You do **not** need Gradle installed as the command uses the wrapper.

   ```
   ./gradlew :spring-session-sample-boot-redis:bootRun
   ```

   You can prove the session is cached in Redis by stopping/restarting the app. It will still have your session stored.

   You can check that the session key is in Redis via the Redis CLI:
   ```
   redis-cli keys '*'
   ```

   You can then delete the key and refresh the page to again prove that the session was backed by Azure Redis. To delete the session, run the following command:./gradlew :spring-session-sample-boot-redis:build -x integrationTest
   ```
   redis-cli keys '*' | xargs redis-cli del
   ```

## Deploy to Azure

The previous Gradle command ran the Spring Boot application but it did not build a deployable archive. To build a deployable `.jar`, we can use the following command. (We skip integration tests because they require starting Docker and the extra time is unnecessary for a quick demo application).

```
./gradlew :spring-session-sample-boot-redis:build -x integrationTest
```

Since this project isn't configured using Maven, we need to deploy the compiled `.jar` using Zip-deploy. I use a Windows machine, so I use the Powershell command below. Pleas see the [Zip-deploy documentation.](https://docs.microsoft.com/en-us/azure/app-service/deploy-zip)

```
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

The archive we want to deploy will be under `______`.
