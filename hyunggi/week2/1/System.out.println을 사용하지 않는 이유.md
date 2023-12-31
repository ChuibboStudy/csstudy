<!-- TOC -->

* [1. System.out.println 메소드는 현업에서 절대 쓰지 말라고하는 메소드인데요. 그 이유가 무엇일까요?](#1-systemoutprintln-메소드는-현업에서-절대-쓰지-말라고하는-메소드인데요-그-이유가-무엇일까요)
  * [1-1. 동기화 키워드가 프로덕션에서 심각한 성능 저하를 일으킬 수 있는 이유는 무엇입니까?](#1-1-동기화-키워드가-프로덕션에서-심각한-성능-저하를-일으킬-수-있는-이유는-무엇입니까)
  * [1-2. Blocking IO는 왜 성능을 저하시킬 수 있을까요?](#1-2-blocking-io는-왜-성능을-저하시킬-수-있을까요)
  * [1-3. 동기화와 차단 IO가 만나면 얼마나 큰 성능 저하가 발생합니까?](#1-3-동기화와-차단-io가-만나면-얼마나-큰-성능-저하가-발생합니까)

<!-- TOC -->

# 1. System.out.println 메소드는 현업에서 절대 쓰지 말라고하는 메소드인데요. 그 이유가 무엇일까요?

- `System.out.println`은 `synchronized` 를 사용하고 있어서 멀티 쓰레드에서 사용시에 애플리케이션 속도가 느려질 수 있다. 높은 처리량이 필요한 애플리케이션에는 효율적이지 않다.

  ``` java
  public void println(String x) {
    synchronized (this) {
        print(x);
        newLine();
    }
  }
  ```

- 로깅을 하는데 있어서 유연성이 부족하다.
  - 로깅 프레임워크와 달리`System.out.println`은 로깅 수준(예: DEBUG, INFO, ERROR)에 대한 제어 또는 출력을 다른 대상으로 리디렉션하는 기능을 제공하지 않는다.
- 언제, 어떤 쓰레드에서 호출했는지 찾기가 힘들다. → 디버깅이 까다롭다.

## 1-1. 동기화 키워드가 프로덕션에서 심각한 성능 저하를 일으킬 수 있는 이유는 무엇입니까?

- 스레드 경합
  - Java의 `synchronized` 키워드는 스레드 동기화에 사용되어 한 번에 하나의 스레드만 동기화된 블록이나 메소드에 액세스할 수 있도록 합니다.
  - 여러 스레드가 동기화된 블록에 액세스하려고 하면 차례를 기다려야 합니다. 특히 동시성이 높은 환경에서는 이러한 대기로 인해 상당한 지연이 발생할 수 있습니다.
- 컨텍스트 전환 오버헤드
  - 스레드가 잠금을 획득하거나 해제할 때마다 시스템은 리소스 집약적인 작업인 컨텍스트 전환을 수행해야 합니다.

## 1-2. Blocking IO는 왜 성능을 저하시킬 수 있을까요?

- 리소스 활용도 부족
  - 스레드가 I/O 작업이 완료되기를 기다리면서 Blocking되는 동안 다른 작업을 수행할 수 없으므로 CPU 리소스 활용도가 낮습니다.
- 지연 시간 증가
  - I/O 작업, 특히 네트워크 또는 디스크 작업은 CPU 작업보다 훨씬 느립니다.

## 1-3. 동기화와 차단 IO가 만나면 얼마나 큰 성능 저하가 발생합니까?

- 동기화 때문에 여러 쓰레드가 동시에 접근할 경우 늦게 온 쓰레드들은 먼저 온 쓰레드가 작업을 마칠때까지 기다려야 합니다.
  그리고 먼저 온 쓰레드에 Blocking IO가 있다면 해당 Blocking IO 작업이 끝날때까지 해당 쓰레드는 기다려야 하는데, 이때 뒤에서 기다리고 있는 쓰레드들도 같이 기다려야 해서 큰 성능 저하가
  발생합니다.
