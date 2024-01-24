### 1. 컨텍스트 스위칭 시에는 어떤 일들이 일어나나요?

## Conext Switch
- Context Switch는 cpu를 한 프로세스에서 다른 프로세스로 넘겨주는 과정을 말한다. 프로세스A에서 프로세스B로 cpu제어권이 넘어가는 상황이라면, 프로세스A의 상태를 pcb에 저장하고, 새로운 프로세스의 pcb를 읽어서 복구하는 작업이 이루어진다.
- 프로세스마다 pcb를 가지고 있고, 운영체제는 운영체제의 data영역에 각 프로세스들의 pcb를 넣어 프로세스를 관리한다.
- (여기서 핵심은, 한 프로세스에서 다른 프로세스로의 전환이라는 것!)

- 참고) 프로세스가 생성되면, 메모리 공간이 생기고, 프로세스별로 메모리 공간에 user stack(메모리 공간 내에 stack)과 kernel stack이 생성된다.
- pcb에는 커널스택을 가리키는 포인터가 있다.
- context-switch가 일어날때마다 페이지 테이블도 바뀌기때문에, pcb에 커널 스택을 가리키는 포인터를 저장해두면, 
- 나중에 프로세스가 변경될 때, 해당 포인터에 해당하는 kernel stack이 레지스터를 복원해두면, 저장했던 정보가 복구되는 원리이다.

## Mode Switch
  - user mode와 kernel mode사이의 전환으로, running state는 그대로 유지된다. (프로세스의 상태가 변화하지 않음)
    - 즉, 같은 프로세스 사이에서 mode switch일 경우, 모드만 변화한 것이지 running state는 바뀌지 않는다는 것을 의미한다.

### 2. 프로세스와 스레드는 컨텍스트 스위칭이 발생했을 때 어떤 차이가 있을까요?

- thread간의 context switch는 캐시 메모리를 초기화할 필요가 없고 레지스터값도 새롭게 설정할 필요가 없어서 오버헤드가 적다.
- thread간의 context switch는 thread간 자원을 공유하기때문에 동기화문제가 발생할 수 있다.
- 프로세스간의 context switch는 캐시 메모리를 초기화해야하며, 레지스터값도 새롭게 설정하는 등의 과정이 필요해서 오버헤드가 크다.
- 하지만 프로세스간의 context switch는 하나의 process가 죽더라도 다른 process에 영향을 주지 않으므로 안정성이 높다.

### 3. 컨텍스트 스위칭이 발생할 때, 기존의 프로세스 정보는 커널스택에 어떠한 형식으로 저장되나요?
- Kernel Stack
  - 프로세스는 user stack과 kernel stack을 가지고 있다.
  - 이들을 분리하는 이유는 mode분리와 마찬가지로 system을 보호하기 위함이다.

<img src="../image/suhyun/kernel-stack.PNG">

- 좌측 위부터, 프로세스p가 실행한다.
- 프로세스p에서 context switch를 유발하는 interrupt or exception or systemcall이 발생하였다.
- pcb0에 p의 state를 저장한다. (커널에서 발생함)
- pcb1에 프로세스q를 reload (커널에서 발생함)
- 프로세스q를 실행한다.

**이때, context switch는 mode switch를 필연적으로 발생시키지만, mode switch는 context switch를 필연적으로 발생시키지 않는다.** 



- 참조 블로그
  - https://mangstor.tistory.com/m/11

### 4. 컨텍스트 스위칭은 언제 일어날까요?
- Context Switch는 cpu를 점유하고 있는 사용자 프로세스A가 timer interrupt나 I/O요청을 위한 system call을 하여 커널 모드로 바꾼 후, 다른 사용자 프로세스B로 cpu를 넘겨줄 때 일어난다.
