# Map.Entry 인터페이스

아래는 자바의 `Map.Entry<K, V>` 인터페이스에 대한 주석 내용을 한글로 쉽게 풀어서 설명한 것입니다.

---

## Map.Entry 인터페이스란?

- **키-값 쌍**: `Map.Entry<K, V>`는 맵에 저장되는 하나의 **키와 값의 한 쌍**을 나타냅니다.
    
- **수정 가능 여부**:
    
    - 일부 Entry는 **수정 불가능**할 수 있습니다.
    - 또는, `setValue` 메서드가 구현되어 있다면 **값을 수정할 수** 있습니다.
- **맵과의 연결(Backed vs. Snapshot)**:
    
    - **백킹(연결된) Entry**:
        - 맵의 `entrySet()`을 순회(Iterator, enhanced for문 등)하여 얻은 Entry는 보통 **백킹 맵(backing map)**과 연결되어 있습니다.
        - 이 경우, 순회하는 동안 `setValue`로 값을 수정하면 **맵에도 그 변경이 반영**됩니다.
        - 단, 이 연결은 **순회하는 동안에만 보장**되며, 순회가 끝난 후 또는 맵이 순회 도중 다른 방식으로 수정되면 Entry의 동작은 **정의되지 않습니다**.
    - **독립된(스냅샷) Entry**:
        - `parallelStream`, `stream`, `toArray` 등 다른 방법으로 얻은 Entry는 **백킹 맵과의 연결 여부**나 `setValue` 지원 여부가 구현체마다 다를 수 있습니다.
        - 또한, `NavigableMap`과 같이 맵의 다른 메서드를 통해 얻은 Entry는 **일반적으로 수정할 수 없는 스냅샷**이며, 맵과 연결되어 있지 않습니다.
        - 직접 생성(`new AbstractMap.SimpleEntry` 또는 `Map.entry`/`Map.Entry.copyOf` 사용)한 Entry 역시 **맵과 연결되어 있지 않은 독립 객체**입니다.
- **정리하면**:  
    `Map.Entry`를 어떻게 획득하느냐에 따라 아래와 같은 특징이 있습니다.
    
    - **백킹 Entry (연결된)**:
        - 맵의 `entrySet()` 순회 중에 얻은 Entry
        - 순회하는 동안에만 안전하게 사용할 수 있음
        - `setValue`를 호출하면 백킹 맵에 반영됨
    - **독립된 Entry (스냅샷)**:
        - 스트림, 배열 변환, 혹은 직접 생성한 Entry
        - 백킹 맵과 연결되어 있지 않으므로, 이후 맵이 변경되어도 Entry의 값은 변하지 않음
        - 보통 `setValue`가 지원되지 않거나, 호출해도 맵에 영향을 주지 않음

---

## 언제, 어디서 사용할까?

- **맵의 내용을 순회하며 읽거나 수정할 때**:  
    `entrySet()`을 사용하여 맵의 모든 키-값 쌍에 접근할 수 있습니다. 예를 들어, 조건에 맞는 엔트리의 값을 변경하고 싶을 때 `setValue` 메서드를 사용할 수 있습니다.
    
- **변경이 일어나지 않는 스냅샷이 필요할 때**:  
    맵의 현재 상태를 보존해야 하거나, 이후 백킹 맵이 변경되더라도 동일한 데이터를 유지하고 싶을 때 `Map.Entry.copyOf` 같은 메서드를 사용하여 스냅샷을 만들 수 있습니다.
    
- **멀티스레드 환경**이나 **백킹 맵의 변경에 따른 부작용**을 피하고자 할 때, 스냅샷 형태의 Entry를 사용하면 안전하게 데이터를 읽을 수 있습니다.
    

---

## 예제 코드

아래 예제 코드는 두 가지 경우를 보여줍니다.

1. **백킹 Entry**: `entrySet()`을 사용하여 순회하면서 값을 수정하는 경우
2. **스냅샷 Entry**: `Map.Entry.copyOf`를 사용하여 백킹 맵과 연결되지 않은 스냅샷을 만드는 경우

```java
import java.util.HashMap;
import java.util.Map;

public class MapEntryExample {
    public static void main(String[] args) {
        // 1. 맵 생성 및 초기화
        Map<String, Integer> map = new HashMap<>();
        map.put("apple", 10);
        map.put("banana", 20);
        map.put("cherry", 30);

        // 2. entrySet()을 이용하여 백킹 Entry를 얻고 출력 (수정 전)
        System.out.println("=== 수정 전 맵 내용 ===");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }

        // 3. 백킹 Entry를 순회하며 "banana"의 값을 수정 (setValue 호출)
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            if ("banana".equals(entry.getKey())) {
                entry.setValue(25); // 이 변경은 백킹 맵에도 반영됨
            }
        }

        System.out.println("\n=== 수정 후 맵 내용 ===");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }

        // 4. Map.Entry.copyOf를 사용하여 스냅샷 Entry를 생성 (Java 10 이상)
        //    이 경우 생성된 Entry는 백킹 맵과 연결되어 있지 않습니다.
        System.out.println("\n=== 스냅샷 (copyOf 사용) ===");
        map.entrySet().stream()
            .map(Map.Entry::copyOf)
            .forEach(e -> System.out.println(e.getKey() + " : " + e.getValue()));

        // 5. 백킹 맵에 새로운 항목 추가 (스냅샷과는 별개로 변경)
        map.put("date", 40);
        System.out.println("\n=== 새로운 항목 추가 후 스냅샷 ===");
        map.entrySet().stream()
            .map(Map.Entry::copyOf)
            .forEach(e -> System.out.println(e.getKey() + " : " + e.getValue()));

        // 참고: 위의 스냅샷은 매번 map.entrySet()의 현재 상태를 복사하여 출력하기 때문에,
        // 백킹 맵이 변경될 때마다 스냅샷 내용도 달라질 수 있습니다.
        // 만약 한 번 복사한 스냅샷을 보존하고 싶다면, 리스트나 다른 컬렉션에 저장하면 됩니다.
    }
}
```

### 코드 설명

1. **맵 생성 및 초기화**:
    
    - `HashMap`에 세 개의 항목을 추가합니다.
2. **백킹 Entry 사용**:
    
    - `for-each` 문을 사용하여 `map.entrySet()`의 각 Entry를 순회하며 출력합니다.
    - 이때 얻은 Entry는 백킹 맵과 연결되어 있어서, 이후 `setValue`로 값을 변경하면 원래 맵의 값도 바뀝니다.
3. **Entry 값 수정**:
    
    - `"banana"`라는 키를 가진 Entry의 값을 `25`로 수정합니다.
    - 수정 후 다시 순회하여 변경된 값을 확인합니다.
4. **스냅샷 Entry 사용**:
    
    - `stream()`과 `map(Map.Entry::copyOf)`를 이용하여 현재 맵의 엔트리들을 복사(스냅샷)합니다.
    - 이 스냅샷 Entry는 백킹 맵과 연결되어 있지 않으므로, 이후 맵이 변경되더라도 스냅샷의 값은 그 당시의 값으로 유지됩니다.
    - 다만, 위 예제에서는 매번 `map.entrySet()`에서 스냅샷을 새로 생성하기 때문에, 백킹 맵이 변경되면 출력 결과에도 변화가 반영됩니다.
        - 만약 한 번의 스냅샷을 고정시켜두고 싶다면, 복사한 결과를 리스트 등 컬렉션에 저장해야 합니다.
5. **새로운 항목 추가**:
    
    - 맵에 `"date"` 항목을 추가한 후, 다시 스냅샷을 출력하여 변경 사항을 확인할 수 있습니다.

---

## 결론

- `Map.Entry`는 **맵의 키와 값의 쌍**을 다루기 위한 인터페이스로,  
    **순회 중 수정이 필요한 경우**나,  
    **현재 상태의 스냅샷이 필요한 경우** 등 상황에 따라 유용하게 사용됩니다.
- 사용 시 주의할 점은, Entry가 **백킹 맵과 연결되어 있는지** 또는 **독립적인 스냅샷**인지에 따라 **동작(예: 수정 시 반영 여부)**이 달라진다는 것입니다.

이처럼 `Map.Entry`를 적절히 활용하면 맵의 데이터를 효율적으로 읽고 수정할 수 있습니다.

