# Iterator Pattern

- [Iterator Pattern](#iterator-pattern)
- [설명](#설명)
  - [장점](#장점)
  - [단점](#단점)
- [구현](#구현)
  - [구조](#구조)
- [참고](#참고)

# 설명

이터레이터 패턴이라고 불리고, 행위 패턴 중 하나이다. (행위 패턴이 궁금하다면 [여기](https://velog.io/@cham/Design-Pattern-%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80)를 보세용)

컬렉션 구현 방법을 노출시키지 않으면서도 그 집합체 안에 들어있는 모든 항목에 접근할 수 있게 해주는 방법을 제공한다. 자료구조에 상관없이 객체 접근 방식을 통일시키고자 할 때 사용된다.

```java
for(int i=0;i<arr.length;i++)

{
System.out.println(arr[i]);
}
```

- 이렇게 `for` 문으로 반복문을 돌리면 arr의 타입이 바뀔 때 수정을 해야한다. 이것을 해결하기 위해 `Iterator`을 사용한다.

## 장점

- 집합체 클래스의 응집도를 높여줌
- 집합체 내에서 어떤 식으로 일이 처리되는지 알 필요없이 모든 항목에 접근할 수 있게 해줌
- 집합체의 인터페이스 및 구현이 간단해지고, 집합체에서는 반복 작업에서 손을 떼고 원래 할 일에 전념할 수 있음

## 단점

- 단순한 순회를 구현하는 경우 클래스만 많아져 복잡도가 증가함

# 구현

## 구조

![Untitled](Iterator%20Pattern%20aba6e290baaa44e8b5c25d873fda8767/Untitled.png)

- Iterator
  - 집합체의 요소들을 순서대로 검색하기 위한 인터페이스 정의
- ConcreateIterator
  - Iterator 인터페이스를 구현함
- Aggregate
  - 여러 요소들로 이루어져 있는 집합체
- ConcreateAggregate
  - Aggreagate 인터페이스를 구현하는 클래스

---

![Untitled](Iterator%20Pattern%20aba6e290baaa44e8b5c25d873fda8767/Untitled%201.png)

**Book.class**

```java
public class Book {
    private String name;

    public Book(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

- 한권의 책에 대한 정보를 가지고 있는 클래스

**Aggregate.class**

```java
public interface Aggregate {

    Iterator createIterator();
}
```

- 집합체를 의미하는 인터페이스
- Aggregate는 Iterator 역할을 만들어내는 인터페이스를 결정한다.

**BookShelf.class**

```java
public class BookShelf implements Aggregate {
    private Book[] books; // 책의 집합
    private int last = 0; // 마지막 책이 꽂힌 위치

    public BookShelf(int size) {
        books = new Book[size];
    }

    public Book getBook(int index) {
        return books[index];
    }

    public int getLength() {
        return last;
    }

    // 책꽂이에 책을 꽂는다
    public void appendBook(Book book) {
        if (last < books.length) {
            this.books[last] = book;
            last++;
        } else {
            System.out.println("책꽂이가 꽉 찼습니다!");
        }
    }

    @Override
    public Iterator createIterator() {
        return new BookShelfIterator(this);
    }
}
```

- 책을 보관하는 책꽂이 역할을 하는 클래스
- BookShelf는 이러한 Aggregate의 구현체이다.
- 실제로 책꽂이 안을 돌아다닐 Iterator를 생성하고 책꽂이를 관리하는 역할을 한다.

**BookShelfIterator.class**

```java
public class BookShelfIterator implements Iterator<Book> {
    private BookShelf bookShelf; // 검색을 수행할 책꽂이
    private int index = 0; // 현재 처리할 책의 위치

    public BookShelfIterator(BookShelf bookShelf) {
        this.bookShelf = bookShelf;
    }

    @Override
    public boolean hasNext() {
        return index < bookShelf.getLength();
    }

    @Override
    public Book next() {
        Book book = bookShelf.getBook(index);
        index++;
        return book;
    }
}
```

- BookShelf 클래스에서 검색을 수행하는 클래스
- BookShelfIterator를 Iterator로 다루기 위해 Iterator 인터페이스를 상속받는다.

**Main.class**

```java
     public static void main(String[] args) {
        BookShelf bookShelf = new BookShelf(10);

        Book book1 = new Book("문학동네");
        Book book2 = new Book("너무 한낮의 연애");
        Book book3 = new Book("늑대의 문장");

        bookShelf.appendBook(book1);
        bookShelf.appendBook(book2);
        bookShelf.appendBook(book3);

        System.out.println("현재 꽂혀있는 책 : " + bookShelf.getLength() + "권");

        Iterator it = bookShelf.createIterator();
        while (it.hasNext()) {
            Book book = (Book) it.next();
            System.out.println(book.getName());
        }
    }

```

실행결과

```java
>>>현재 꽂혀있는 책 : 3권
>>>문학동네
>>>너무 한낮의 연애
>>>늑대의 문장
```

- BookShelfIterator는 책꽂이에서 책을 한권씩 뽑아오는 역할을 한다.
- 검색할 책이 존재하는 동안 while문이 수행되며, it.next()에 의해 한권씩 책을 꺼내 이름을 출력한다.

# 참고

[[Design Pattern] 이터레이터 패턴 (iterator pattern)](https://velog.io/@cham/Design-Pattern-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8C%A8%ED%84%B4-iterator-pattern)

[08 반복자 패턴 (Iterator Pattern)](https://lktprogrammer.tistory.com/40)
