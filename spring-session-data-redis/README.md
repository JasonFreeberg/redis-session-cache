# Configuration Instructions
> Notes for Ready Presentation

The bit, `spring.security.user.password=password`, in `application.properties` is literally the password to use in the form.

Add this Bean to `web.http.RedisHttpSessionConfiguration` so the Redis client does not invoke the `CONFIG` command. [Documented here.](https://docs.spring.io/spring-session/docs/current/reference/html5/#api-redisoperationssessionrepository-sessiondestroyedevent)

```
@Bean
public static ConfigureRedisAction configureRedisAction() {
    return ConfigureRedisAction.NO_OP;
}
```

You can prove the session is cached in Redis by stopping/restarting the app. It will still have your session stored.

You should then run the CLI to show that the key is there.
