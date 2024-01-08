## Blocking IO
- I/O 작업이 진행되는 동안 프로세스(쓰레드)가 작업을 중단한채, I/O가 끝날 때 까지 대기하는 방식
![image](https://github.com/ChuibboStudy/csstudy/assets/81959996/84f492a7-aa3f-4c5e-a0a5-2dcd174469fd)

## Non Blocking IO
- I/O 작업을 호출했을 때, 완료될 때 까지 기다리지 않고 즉시 리턴.
- 이후 다른 작업을 수행하다 중간마다 I/O 결과를 물어보며, 결과를 받는다.
![image](https://github.com/ChuibboStudy/csstudy/assets/81959996/80a774ca-69e0-4efa-b0bf-542e0f51e84e)
