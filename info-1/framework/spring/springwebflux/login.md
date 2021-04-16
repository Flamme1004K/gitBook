# login



```text
private fun login(userId: String, pwd: String?, type: CommonType?) : String? {
    try {
        return WebClient.create(CommonData.)
            .post()
            .uri { builder ->
                builder.path(CommonData.)
                    .queryParam("", userId)
                    .queryParam("", pwd)
                    .queryParam("", type).build()
            }
            .retrieve().bodyToMono(String::class.java)
            .timeout(Duration.ofMillis(3000))
            .block()
    } catch (e:TimeoutException) {
        throw CustomException(ResultCode.UnknownError)
    }
}
```

