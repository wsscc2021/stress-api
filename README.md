## REST API
요청을 보냈을 때 서버에 CPU 사용률이 급격하게 증가하는 간단한 API 서버입니다.
HPA, 모니터링 등의 CPU 사용률 변화에 따른 동작이 필요할 때 유용하게 사용할 수 있습니다.

### GET /foo
Response
```
{
    "name": "foo",
    "version": "<version>",
    "message": "Bonjour!"
}
```


### GET /foo/key
Query String
| key  | type   | description          |
| ---- | ------ | -------------------- |
| name | string | 단순 식별 *생략 가능  |

Response
```
{
    "name": <name>,
    "key": "<1024 size random key>
}
```

### CPU 사용률이 급격하게 증가하는 원리
Ref: https://javacan.tistory.com/entry/41

Java에서 String을 합치는 연산을 수행할 때 + 연산자를 사용하면 상당한 비효율을 발생시킨다.
이 점을 이용하여 1024길이의 랜덤 문자열을 생성할 때 `+` 연산자를 1024번 이용하도록 구현했고, 해당 로직에서 연산처리가 급격하게 늘어난다.

```
for(int i=0; i<1024; i++) {
    keyString += getRandomAlphaNumberic(1);
}
```



### GET /foo/bot


### Memory 사용률이 급격하게 증가하는 원리

한번 요청이 들어오면 bot인스턴스를 1000만개 만들도록 구현했다.
요청이 처리될 때까지 해당 인스턴스들이 Heap 메모리 영역에 잔존하여 메모리 사용률이 증가하게 된다.

```
public List<BotDto> cloneBotMillion(String botName) {
    List<BotDto> bots = new ArrayList<BotDto>();
    for (int i=0; i<1000000; i++) {
        bots.add(new BotDto().setId(i).setName(botName));
    }
    return bots;
}
```



### 부하 테스트 주는 방법
apache benchmark를 통해 간단하게 생성할 수 있다.

`CPU`
```
ab -c 20 -n 10000 http://{server-ip}:8080/key
```

`Memory`
```
ab -c 20 -n 10000 http://{server-ip}:8080/bot
```