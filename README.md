#### redis 특징
* single thread 기반
* in-memory 기반

#### redis 접속
    * redis-cli를 통해서 접속

#### redis collections
1. strings
    * 기본 사용법
      ```
      set <key> <value>
      get <key>    
      ```

    * 멀티 key 사용법
      ```
      mset <key1> <value1> <key2> <value2> ... <keyN> <valueN>
      mget <key1> <key2> ... <keyN>
      ```

    * 간단한 sql 대체해보기
        * sql
            ```sql
            INSERT INTO users(id, name)
            VALUES (
              'user-id-1'
            , 'user-name-1'
            );
            ```

        * redis - strings
            ```
            //set 이용
            set id:user-id-1 user-id-1
            set name:user-name-1 user-name-1
        
            //mset 이용
            mset id:user-id-1 user-id-1 name:user-name-1 user-name-a
            ```

2. list
    * 기본 사용법
      ```
      Lpush <key> <A>
      Rpush <key> <B>
      Lpop
      Rpop
      ```

    * 예시
      ```
      Lpush key-1 A        //[A]
      Rpush key-1 B        //[A, B]
      Lpush key-1 C        //[C, A, B]
      Rpush key-1 D, A     //[C, A, B, D, A]
      Lpop                 //[A, B, D, A], C
      Rpop                 //[A, B, D], A
      Rpop                 //[A, B], D
      Lpop                 //[B], A
      ```

3. set
    * 기본 사용법
      ```
      SADD <key> <value>
        //value가 이미 존재하는 경우, 추가되지 않는다.

      SMEMBERS <key>
        //모든 값을 리턴한다. O(n) 명령어

      SISMEMBER <key> <value>
        //집합에 해당 값이 있으면 1 없으면 0을 리턴한다.
      ```

4. sorted set
    * 기본 사용법
      ```
      ZADD <key> <score> <value>
        //value가 이미 존재하는 경우 해당 score로 변경된다.

      ZRANGE <key> <inclusive startindex> <exclusive endindex>
        //해당 index 범위 값을 모두 리턴한다.
        //ZRANGE 0 -1 의 경우, 모든 범위를 리턴한다.
        //score 값은 double 타입이기 때문에 값이 정확하지 않을 수 있다.(부동 소수점 표기 한계)
    
      ZREVRANGE <key> <inclusive startindex> <exclusive endindex>
        //ZRANGE의 역순으로 값을 리턴
    
      ZRANGEBYSCORE <key> <inclusive minumum score> <exclusive maximum score>
        //특정 score를 초과한 값들을 리턴하기 위해서는
        //ZRANGEBYSCORE (70 +inf
      ```

5. hash
    * 기본 사용법
      ```
      Hmset <key> <subkey1> <value1> <subkey2> <value2>...
      Hgetall <key>
        //해당 key의 모든 subkey와 value를 가져옴
      Hget <key> <subkey>
      Hmget <key> <subkey1> <subkey2> ... <subkeyN>
      ```
      
    * 간단한 sql 대체해보기
        * sql
            ```sql
            INSERT INTO USERS(id, name)
            VALUES(
              'user-id-1'
            , 'user-name-1'
            );
            ```

        * redis - hash
            ```
            hmset user-id-1 id user-id-1 name user-name-1
            ```

#### collection 주의사항
* 하나의 컬렉션에 너무 많은 아이템을 담지 말 것
    * 보통 10000개 이하 수준으로 유지하는 것이 좋다
    
* expire는 Collection의 item개별로 걸리지 않고 전체 Collection에 대해서만 걸림
    * 특정 collection에 expire가 걸려 있는 경우, 해당 expire 만료시 해당 collection 전체가 사라진다.
    
