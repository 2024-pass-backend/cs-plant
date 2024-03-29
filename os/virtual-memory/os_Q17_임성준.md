## 가상 메모리란 무엇인가요?

가상 메모리는 실행하고자 하는 프로그램을 일부만 메모리에 적재하여 실제 물리 메모리 크기보다 더 큰 프로세스를 실행할 수 있게 하는 기술입니다. 

---

가상의 의미는 각 프로세스마다 실제 물리 메모리가 아닌 가상의 주소 공간을 보이게 한다는 의미이다. ([https://namu.wiki/w/가상 메모리](https://namu.wiki/w/%EA%B0%80%EC%83%81%20%EB%A9%94%EB%AA%A8%EB%A6%AC))
![image](https://github.com/sungjun0629/blog-study/assets/113486696/d85a21e7-6d87-47ff-8c2c-41faa9431592)

## 가상 메모리가 가능한 이유가 무엇일까요?

운영 체제와 하드웨어 간의 상호작용과 논리주소와 물리주소가 분리되어 있기 때문이라고 생각한다. 각 프로세스는 논리 주소를 사용하며, 이는 서로 독립된 메모리 공간을 갖게 합니다. 논리 주소와 물리 주소를 연결하는 매핑을 통해 RAM과 disk에 데이터를 분배할 수 있습니다. 

![image](https://github.com/sungjun0629/blog-study/assets/113486696/8289375a-55cd-4735-9f72-a4a92d198e58)

이를 가능케 하는 가상 메모리 관리 기법에는 **페이징과 세그멘테이션**이 있습니다.

## Page Fault가 발생했을 때, 어떻게 처리하는지 설명해 주세요.

Page Fault(페이지 부재)는 프로그램이 실행 중인 동안 필요한 데이터 또는 코드가 현재 램에 로드되어 있지 않은 상태에서 참조되었을 때 발생합니다.

1. **페이지 부재 발견:**
    - 페이지 테이블 검사 결과 해당 페이지가 현재 램에 없는 경우, 페이지 부재가 발생했다고 판단합니다.
2. **디스크에서 페이지 로드:**
    - 필요한 페이지가 현재 램에 없으면 해당 페이지를 디스크에서 램으로 로드합니다. 이때, 디스크 I/O 작업이 발생할 수 있으며, 이 작업이 상대적으로 느릴 수 있습니다.
3. **페이지 테이블 갱신:**
    - 페이지가 램으로 로드되면 페이지 테이블을 업데이트하여 가상 주소와 램 주소 간의 매핑을 반영합니다.
4. **인터럽트 및 재실행:**
    - 페이지 폴트 처리가 완료되면 인터럽트가 발생하고, 프로세스는 중단된 지점에서 다시 실행됩니다.
    - 프로세스는 이제 필요한 데이터나 코드가 메모리에 로드되어 있으므로 계속 진행할 수 있습니다.

---

![image](https://github.com/sungjun0629/blog-study/assets/113486696/7743e9e9-942f-4910-b946-335da959a13a)


![image](https://github.com/sungjun0629/blog-study/assets/113486696/e35e0236-4356-4b58-832c-2750e19a8c72)

https://eunjinii.tistory.com/142

## 페이지 크기에 대한 Trade-Off를 설명해 주세요.

페이지 크기 선택에는 여러 측면이 관련되어 있습니다.

1. **내부 조각과 외부 조각:**
    - 작은 페이지 크기는 내부 조각을 줄일 수 있습니다. 내부 조각은 페이지 크기보다 작은 데이터가 메모리에 할당되었을 때 발생하는 낭비를 의미합니다. 작은 페이지 크기는 메모리에 더 정확하게 맞아들어갈 수 있어 내부 조각을 최소화할 수 있습니다.
2. **페이지 테이블 크기:**
    - 페이지 테이블은 가상 주소와 물리 주소 간의 매핑 정보를 저장하는 데 사용됩니다. 작은 페이지 크기는 더 많은 페이지를 필요로 하며, 이는 페이지 테이블의 크기를 증가시킬 수 있습니다.
3. **I/O 작업:**
    - 페이지 크기는 디스크에서 메모리로 데이터를 읽거나 쓸 때의 I/O 작업에도 영향을 미칩니다. 작은 페이지 크기는 더 많은 페이지를 디스크에서 읽어와야 하므로 I/O 오버헤드가 증가할 수 있습니다.
    - 반면에 큰 페이지 크기는 더 많은 데이터를 한 번에 읽어올 수 있어 I/O 효율성이 향상될 수 있습니다.
4. **캐시 성능:**
    - 페이지 크기는 캐시 메모리의 성능에도 영향을 미칩니다. 작은 페이지 크기는 더 자주 캐시 미스가 발생할 수 있지만, 큰 페이지 크기는 더 적은 캐시 라인을 사용할 수 있습니다.
5. **페이지 폴트 비용:**
    - 작은 페이지 크기는 페이지 폴트가 자주 발생할 수 있지만, 이는 페이지 폴트 비용이 작아지는 장점을 가질 수 있습니다.
    - 반면에 큰 페이지 크기는 페이지 폴트가 적게 발생할 수 있지만, 페이지 폴트 발생 시 해당 페이지를 디스크에서 읽어오는 비용이 더 크게 될 수 있습니다.


## 페이지 크기가 커지면, 페이지 폴트가 더 많이 발생한다고 할 수 있나요?

일반적으로 페이지 크기가 커질수록 페이지 폴트(Page Fault)가 더 적게 발생하는 경향이 있습니다.

페이지 크기가 커지면 각 페이지에 포함되는 데이터 양이 더 많아집니다. 따라서 특정 데이터에 접근할 때 해당 페이지의 다른 부분에 있는 데이터도 함께 로드될 가능성이 높아집니다.

- 가상메모리는 페이징으로 메모리를 관리하는데, 세그멘테이션은 어떻게 할 수 있을까요?

세그먼테이션은 프로세스를 **논리적 내용을 기반**으로 나눠서 메모리에 배치하는 것을 말한다.

세그먼테이션은 프로세스를 세그먼트(segment)의 집합으로 만들고, 각 세그먼트의 크기는 일반적으로 같지 않다. 프로세스를 code + data + stack 으로 나누는 것 역시 세그먼테이션의 모습이다. 물론 code, data, stack 각각 내부에서 더 작은 세그먼트로 나눌 수도 있다.

![image](https://github.com/sungjun0629/blog-study/assets/113486696/d4614508-21bd-4ee8-a95c-83afc543ef1a)
https://velog.io/@codemcd/운영체제OS-14.-세그멘테이션
