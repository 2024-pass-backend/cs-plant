## 1. Mutex와 Semaphore의 차이점은 무엇인가요? (+Spin lock) + 이진 semaphore와 mutex의 차이?
- spin lock과 semaphore 그리고 mutex는 모두 공유 자원에 대한 동시 접근을 제어하기 위해 쓰이는 lock이다.
  - 공유가 불가능한 자원에 동시접근을 허용한다면, 동시성이 제어되지 않아 의도치 않게 값이 덮어씌어지는 등 데이터가 일관성을 잃게 될 가능성이 생기기 때문이다. 이에 따라 **lock을 통해** 공유 자원에 접근하는 부분, 즉 동시에 접근하면 안되는 구역인 **critical section의 진입을 통제함으로써 일관성을 보장한다.**

- spin lock
  - 임계 구역에 진입할 때까지 cpu를 놓치 않고 계속하여 lock의 취득을 시도하는 busy waiting으로 구현된다.
    - busy waiting
      - 원하는 자원을 얻기 위해 기다리는 것이 아니라, 권한을 얻을때까지 확인하는 것을 의미한다.

- mutex, semaphore
  - busy waiting으로 구현될 수 있으나 spin lock과 다르게 **lock을 요청한 후 취득할 때까지 cpu를 소모하지 않고 잠들어 있도록 wait queue를 사용하도록 구현된다.**

- 금방 임계 구역에 진입이 불가능한 경우
  - 수행가능한 다른 프로세스가 수행되고 있을 수 있도록 cpu를 낭비하지 않는 semaphore나 mutex가 효율적이다.
- 금방 임계 구역에 진입이 가능한 경우
  - 프로세스가 잠들어있다가 깨어나는 context-switch이 필요없는 spin lock이 더 효율적이다.

- semaphore
- lock의 주체와 unlock의 주체가 달라도 된다. 즉, lock의 동시접근만을 제어할 뿐 unlock의 주체가 lock을 취득한 프로세스인지는 확인하지 않는다.
- 이에 따라, lock을 취득했던 프로세스가 lock을 해제하지 않은 채 비정상 종료되었다고 하더라도, semaphore에서는 다른 프로세스가 unlock을 하여 무한히 대기하는 상황을 회피할 수 있다.
  - binary semaphore(이진 세마포어)
    - 임계 구역에 동시 진입을 허용할 프로세스의 수에 따라 1개만 허용
  - counting semaphore(계수 세마포어)
    - binary semaphore가 아닌 그 외에 모든 경우
- mutex
  - 임계 구역에 동시 진입을 허용할 프로세스의 수에 따라 1개만 허용
  - unlock의 주체와 lock의 주체가 같아야한다.
    - 단점 ) mutex를 통해 critical section에 진입한 프로세스가 비정상적으로 종료되거나 무한 루프에 빠진다면
      - 같은 lock을 바라보고 critical section에 진입하려고 시도하고 있던 프로세스는 영원히 진입이 불가능
  
- 참조블로그
  - https://hexoul.github.io/mutex

## 2. Lock을 얻기 위해 대기하는 프로세스들은 Spin Lock 기법을 사용할 수 있습니다. 이 방법의 장단점은 무엇인가요? 단점을 해결할 방법은 없을까요?
- lock을 취득할 때까지 계속해서 확인하기 때문에 cpu를 계속 낭비한다는 단점이 있다.
- 문맥교환이 발생하지 않아 문맥교환으로 인해 발생하는 cpu의 부하를 줄일 수 있다.

- multi core환경이고, critical section의 작업이 context switch보다 더 빨리 끝난다면 mutex보다 spin lock을 이용하도록 한다.

## 뮤텍스와 세마포어 모두 커널이 관리하기 때문에, Lock을 얻고 방출하는 과정에서 시스템 콜을 호출해야 합니다. 이 방법의 장단점이 있을까요? 단점을 해결할 수 있는 방법은 없을까요?
- lock을 이미 스레드가 점유하고 있다면, 다른 스레드는 잠들어야한다. (sleep()시스템 콜)
- lock을 반환했다면, waiting queue에 있던 스레드를 깨우게 된다. (wakeup() 시스템콜)
- 즉, lock을 얻고 방출하는 과정에서, 시스템콜이 호출하게 된다.

- 이를 해결하기 위한 방법으로 try_lock을 구현하는 것이다.
- 일반 lock은 이미 잠겨 있다면, 해당 스레드가 block되지만, try_lock은 이미 잠겨 있다면, 바로 block이 되는 것이 아니라 false를 리턴하기에, 다른 일을 수행할 수 있다.

- 참조블로그
  - https://dad-rock.tistory.com/377
  - https://kukuta.tistory.com/440