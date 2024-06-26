# 교착 상태

교착 상태란 두 개 이상의 프로세스가 서로 상대방의 작업이 끝나기만을 기다리고 있어 무한정 기다리는 상태를 말합니다.  
교착 상태는 다음 네 가지 조건이 모두 성립할 때 발생합니다.  

1. 상호 배제: 자원은 한 번에 한 프로세스만 사용할 수 있어야 한다.
2. 점유와 대기: 프로세스가 자원을 할당 받은 상태에서 다른 자원을 할당 받기 위해 대기하고 있어야 한다.
3. 비선점: 자원을 강제로 빼앗을 수 없어야 한다.  
4. 순환 대기: 프로세스들이 자원을 원형으로 대기하고 있어야 한다.  

## 교착 상태 예방 
교착 상태를 예방 하려면 위 네 가지 조건 중 하나를 제거하면 됩니다.  
하지만 조건을 완전히 제거하는 것은 현실적으로 불가능합니다.  

- 상호 배제 : 모든 자원을 공유 가능하게 만들면 되지만 현실적으로 불가능합니다.  
- 점유와 대기 : 특정 프로세스에 모든 자원을 할당하거나 아예 할당하지 않으면 됩니다.
  - 자원의 활용률을 낮출 수 있는 방법입니다.
- 비선점 : 선점이 가능한 자원에 한해서는 효과적입니다.
  - 모든 자원이 선점 가능하지는 않습니다. ex) CPU
- 순환 대기 : 자원에 번호를 붙이고 오름차순으로 할당하면 됩니다.
  - 자원에 번호를 붙이는 것이 어렵고, 어떤 자원에 어떤 번호를 부여하는지에 따라 자원 활용률이 달라집니다.

## 교착 상태 회피
교착 상태를 회피하기 위해서는 자원을 할당할 때 교착 상태가 발생하지 않을 만큼만 자원을 할당하면 됩니다.  

안전 순서열 : 교착 상태 없이 안전하게 프로세스들에 자원을 할당할 수 있는 순서
안전 상태 : 교착 상태 없이 모든 프로세스가 자원을 할당 받고 종료될 수 있는 상태
불안전 상태 : 교착 상태가 발생할 수도 있는 상태

## 교착 상태 검출 후 회복
교착 상태의 발생을 막는 것은 어렵기 때문에 교착 상태가 발생하면 이를 검출하고 회복하는 방법을 사용합니다.  
일단 교착 상태가 발생하면 시스템을 초기 상태로 되돌리거나 교착 상태를 일으킨 프로세스를 종료시키는 방법을 사용합니다.  
이 때 작업 중인 프로세스의 작업 내역이 모두 사라질 수 도 있고 오버헤드가 발생합니다.  