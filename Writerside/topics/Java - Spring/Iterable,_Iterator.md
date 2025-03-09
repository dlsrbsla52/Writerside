# Iterable, Iterator

`Iterable` : "반복 가능한"이라는 뜻
`Iterator` : "반복자"라는 뜻


### Iterable 인터페이스의 주요 메서드

```JAVA
public interface Iterable<T> {
	Iterator<T> iterator();
}
```

- 단순히 Iterator 반복자를 반환한다.

### Iterator 인터페이스의 주요 메서드
```JAVA
public interface Iterator<E> {
	boolean hasNext();
	E next();
}
```

- hsaNext() : 다음 요소가 있는지 확인한다. 다음 요소가 없으면 false를 반환한다.
- next() : 다음 요소를 반환한다. 내부에 있는 위치를 다음으로 이동한다.

자료 구조에 들어있는 데이터를 처음부터 끝가지 순회하는 방법은 단순하다. 자료 구조에 다음 요소가 있는지 물어보고, 있으면 다음 요소를 꺼내는 과정을 반복하면 된다.
만약 다음 요소가 없다면 종료하면 된다. 이렇게 하면 자료 구조에 있는 모든 데이터를 순회할 수 있다.
