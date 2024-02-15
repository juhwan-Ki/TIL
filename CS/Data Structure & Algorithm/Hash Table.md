### Hash Table
- Hash Table은 효율적인 탐색(빠른 탐색)을 위한 자료구조로써 key-value쌍의 데이터를 입력받는다. hash function h에 key값을 입력으로 넣어 얻은 해시값 h(k)를 위치로 지정하여 key- value 데이터 쌍을 저장한다. 저장, 삭제, 검색의 시간복잡도는 모두 O(1)이다.

- Hash Table은 고정된 배열을 선언 미리 선언한다. 그 후 hash function을 이용해서 key값에 맞는 hash code를 반환하고 hash code를 이용하여 배열의 index에 접근하여 데이터를 저장하고 조회 한다.

### Hash Collision
- Hash Collision이란 서로 다른 key의 해시값이 똑같을 때를 말한다. 즉, 중복되는 key는 없지만 해시값은 중복될 수 있는데 이 때 Collision이 발생했다고 한다. 

