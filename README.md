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
         ID string
         Title string
         Value int
      }


```
##### 간단하게 ID와 Title 그리고 Value 를 갖는 structure 를 정의했다.

```c
go get github.com/syndtr/goleveldb/leveldb
```
##### leveldb package 를 사용하기 위해 해당 패키지를 다운받는다.


```go
data := TestStruct{"gohs091", "LeveldbTest", 1}
// levelDB에 넣을 데이터를 만들고

db, err := leveldb.OpenFile("path/to/db", nil)
// 

err = db.Put([]byte("key"), []byte("value"), nil)



