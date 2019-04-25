# fabricStudy
Hyperledger fabric study 

## Level DB Package 를 Go 언어로 실습해보자.


##### 필요한 Package 를 import 해줘야 한다.
```go
import (
        "encoding/json"
        //직렬화(Serialization : Json -> byte[]), 역직렬화(Deserialization : byte[] -> Json)
        //을 사용하기 위해 필요한 패키지 
        
        "fmt"
        // 표준 입출력 함수들을 사용하기 위해 필요한 패키지
        
        "github.com/syndtr/goleveldb/leveldb"
        // level DB 를 사용하기 위한 패키지
)
```



##### DB에 넣을 Data type 을 정의하자 
```go

type TestStruct struct {
        Title string
        Value int
        ID string
}
 
