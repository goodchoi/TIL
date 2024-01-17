# 📍main topic : Thread Safety와 동기화(synchronization)
> ✅ 주요 키워드 : lock, synchronized, deadlock, ThreadLocal, etc



volitle : cpu cache거치지 않고 직접 메모리에 접근

동기화
+ blocking: 특정스레드가 작업을 수행하는동안 다른작업은 진행하지않고 대기하는 방식
  + ex) Monitor, Syncronized키워드 
  + dead lock, priority inversion
+ nonblocking: 다른스레드의 작업여부와 상관없이 자신의 작업을 수행하는 방식
  + CAS 알고리즘을 사용한 Atomic type
    + Atomictype: 동시성을 보장하기위해서 자바에서 제공하는 Wrapper Class
    내부적으로 `CAS`알고리즘과 `Volatile`을 사용
    + live lock(지속적인 재시도)

process, thread, syncronized ,wait,notify


