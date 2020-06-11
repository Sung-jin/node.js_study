# Multi Thread

- process - 운영체제에서 실행 중인 하나의 애플리케이션
- multi tasking
  - 두개 이상의 작업을 동시에 처리하는 것 (애플리케이션 간 단위)
  - 운영체제에서 할당받은 독립적인 메모리상에서 동작한다.
- thread (애플리케이션 내부)
  - 하나의 프로세스가 두개 이상의 작업을 처리할 수 있는데, 이때 multi thread 를 사용하면 된다.
  - thread 1개를 하나의 코드 실행 흐름을 가지며, 2개 이상의 thread 가 존재한다면 그 thread 수 만큼 코드 실행의 흐름이 생기게 된다.
  - 하나의 프로세스 안에서 생성되기 때문에 thread 간에 영향을 줄 수 있으며, 예외가 발생하면 다른 thread 에도 영향을 줄 수 있다.

## Main Thread

- 자바를 기준으로 애플리케이션은 main thread 가 main() 메소드를 실행하면서 동작한다.
- 이러한 main thread 는 thread 를 생성하고 main 의 흐름이 진행되면서 생성한 thread 는 독릭접으로 실행될 수 있으며, 생성한 thread 에서도 다시 thread 를 생성할 수도 있다.
  - 위 방법은 단순히 thread 안에 thread 안에 thread... 이러한 형태는 추천하지 않는데, thread 가 생성되면 독립적으로 실행이되고 다른 thread 에 영향을 미칠 수 있는데 계층구조가 계속 된다면 어디 thread 에 의해 에러가 발생하고 어떤 현상에 의해 문제가 발생하는지 추적하기 힘들기 때문에, 계층을 이뤄야만 한다면 설계를 아주 잘 해야만 한다.
  - 생성한 thread 를 부모 thread, 생성 당한 thread 를 자식 thread 라고 하겠다.
- 자식 thread 는 부모 thread 의 동작이 종료되었다고 하더라도 자식 thread 의 동작이 남아있다면 종료되지 않고 계속 동작한다.

## 스레드 생성

- 메인 작업 외에 추가적인 병렬 작업의 수만큼 thread 를 객체로 생성하여 동작시키면 된다.
  - **객체로 생성**되므로, java.lang.Thread 클래스를 직접 객체화해서 생성해도 되고, Thread 를 상속해서 하위 클래스를 만들어 생성할 수도 있다.
- java.lang.Thread 클래스로 직접 작업 스레드 생성하기 위해서는 Runnable 을 매개값으로 가지는 생성자를 호출해야 한다.
  - Runnable 은 작업 스레드가 실행하는 흐름, 작업, 코드 라고 생각하면 된다. 즉, Thread 에서 동작하는 하나의 로직이라고 생각하면 된다.
  - Runnable 에는 run() 메소드가 하나 정의되어 있으며, Runnable 구현 클래스는 run() 을 재정의해서 작업 스레드가 샐행할 코드를 작성하면 된다.
  - Runnable 클래스를 구현한 클래스를 사용해도 되지만, 익명 클래스를 통해 thread 에서 동작할 로직을 바로 넘겨서 동작시킬 수 있다.
  - Runnable 인터페이스는 run() 메소드 하나만 있기 때문에, 함수적 인터페이스이며 Java8 이상부터 람다식을 매개값으로 사용할 수 있다.
- 실행은 생성한 thread 객체의 start() 메소드를 통해 매개값으로 넣은 Runnable 의 run() 메소드를 실행시킬 수 있다.

```JAVA
class Task implements Runnable {
    public void run() {
        print("Hello world~");
    }
}

...
Runnable task = new Task();
Thread thread1 = new Thread(task);
// 생성한 부모 thread 와 별개로 동작하는 thread 가 생성되며, Hello world 를 콘솔에 출력한다.
Thread thread2 = new Thread(new Runnable() {
    @Override
    public void run() {
        print("Bye world~");
    }
});
Thread thread3 = new Thread(() -> {
    print("execute code by lambda");
});

thread1.start(); // Hello world~
thread2.start(); // Bye world~
thread3.start(); // execute code by lambda
```

- Runnable 을 통해 thread 가 실행할 작업을 정의하지 않고, Thread 의 하위 클래스로 작업 스레드를 직접 정의하여 작업 내용을 포함시킬 수 있다.
  - Thread 를 상속받고, run() 을 overriding 하여 thread 가 동작할 코드를 직접 작성하여 정의한 클래스를 객체로 생성하고 start() 하면 된다.
- 또는 Thread 에 Runnable 을 익명 객체로 생성하여 매개값으로 넘겨준 것 처럼, Thread 의 run() 을 익명 객체로 넘겨줘서 선언 및 사용할 수 있다.

```JAVA
public class WorkerThread extends Thread {
    @Override
    public void run() {
        print("직접 정의한 thread~");
    }
}

...

Thread thread1 = new WokerThread();
thread1.start(); // 직접 정의한 thread~

Thread thread2 = new Thread() {
    public void run() {
        print("thread 의 run 익명 객체");
    }
}
thread2.start(); // thread 의 run 익명 객체
```

## Thread setName

- main 의 Thread 이름은 main 이라는 이름을 가지고 있으며, 생성한 Thread 의 이름은 자동으로 Thread-n 이라는 이름을 가지게 된다.
- 디버깅의 콘솔에 Thread 의 이름을 설정해주고 확인하고 싶다면, Thread 에 setName 을 해주면 된다.
- getName() 을 통해 Thread 의 이름을 얻을 수 있다.
- currentThread() 라는 Thread 의 정적 메소드가 존재하며, 이는 currentThread() 를 호출한 그 Thread 의 참조를 리턴해준다.

```JAVA
Thread thread = new Thread(task);
thread.setThread("foo");
print(thread.getName()); // foo
```

## Thread? Runnable?

- Thread 를 상속받거나 Thread 클래스를 직접 이용하는 방법과 Runnable 인터페이스를 이용하는 2가지 방법이 있다.
- 비슷해 보이면서 조금 다르고, 내부적으로 동작하거나 구현되어있는 형태가 비슷한데 왜 2가지로 나눠져 있을까?
  - 동작은 같다. 동작은.
  - 자바에서 다중 상속 (Thread 를 상속받아서 run 을 직접 Override 하는 경우) 을 지원하지 않지만, 다중 구현 (Ruunable 을 implement) 은 가능하다.
  - 즉, Runnable 을 구현하는 형태로 클래스를 만든다면, 그 클래스는 다른 클래스를 상속을 받을 수 있으며 다른 인터페이스를 구현할 수도 있다.
  - 고로 확장성이 좋아지고 대부분의 개발자들은 Runnable 클래스를 구현하는 것을 선호한다.
  - 근데 또, 익명 매개변수를 넘겨주는 방법도 있으니.. 판단은 개발자가 상황에 따라 하면 될 듯 하다. 이부분은 조금 토의를 했으면 한다.

## Concurrency (동시성)

- 멀티 작업을 위해 하나의 코어에서 멀티 스레드가 번갈아가면서 실행되는 성질.
- Single core CPU 환경에서 멀티 스레드 작업은 병렬성처럼 보이지만, 서로 번갈아 돌아가면서 작업되는 동시성 작업이다. 다만, 수행속도가 빨라서 병렬성처럼 보일뿐이다.
- 동시성으로 Thread 를 동작하게 한다면, 하나의 동작만 해야 하므로 Thread 간의 작업 순서를 정하고 돌아가면서 동작해야 하는데, 이를 스케쥴링이라 한다.
- 자바의 스케쥴링 방식
  1. Priority (우선순위 방식)
     - 우선순위가 높은 Thread 가 더 많이 실행되는 방식
     - Thread 객체에 개발자가 직접 순위를 설정할 수 있다.
     - 10 -> 1 으로 설정할 수 있으며, 10이 가장 높은 우선순위를 가지며, setPriority() 메소드를 이용하면 된다.
     - defualt 값은 5 이다.
     - 싱글 코어 CPU 에서는 우선순위에 따라서 더 많이 동작되지만, 멀티 코어에서는 그 코어 수만큼 병렬성으로 실행 될 수 있기 때문에, 코어 수보다 적은 Thread 가 실행되면 우선순위에 상관없이 모두 동시에 Thread 가 실행된다.
  2. Round-Robin (순환 할당)
     - 시간 할당량을 정해서 하나의 스레드를 정해진 시간만큼 실행하고 다른 Thread 를 실행하는 방식
     - JVM 에 의해 정해지기 때문에 코드로 제어할 수 없다.

```JAVA
thread.setPriority(1..10);
thread.setPriority(Thread.MAX_PRIORITY);    // 10
thread.setPriority(Thread.NORM_PRIORITY);   // 5
thread.setPriority(Thread.MIN_PRIORITY);    // 1
```

## Parallelism (병렬성)

- 멀티 작업을 위해 멀티 코어에서 개별 스레드를 동시에 실행하는 성질.

## 객체 공유

- 멀티 스레드 프로그램에서는 스레드들이 객체를 공유해서 작업해야 하는 경우가 존재한다.
- 공유해서 객체를 사용하게 되면, 각 Thread 가 동작할 때 그 객체가 같다고 보장을 할 수 없으며 이를 위해서 동기화가 필요하다.
  - A, B thread 에서 FOO 라는 객체를 사용해야 한다면 A 가 FOO 를 변경 한 시점에 B 가 FOO 변경한다면 A와 B 의 FOO 라는 객체는 같은 값을 보장 할 수 없다. 그리고 그로인해 원치않는 결과가 발생할 것이다.

```JAVA
public class SharedThread extends Thread {
    private int FOO = 0;
    private int sleepTime = 0;

    @Override
    public void run() {
        print(FOO);
        Thread.sleep(sleepTime);
        print(FOO);
    }

    public setFOO(int num) {
        this.FOO += num;
    }

    public setSleepTime(int num) {
        this.sleepTime = num;
    }
}

Thread thread1 = new SharedThread();
Thread thread2 = new SharedThread();

thread1.setTime(2000);
thread2.setTime(1000);

thread1.start();
thread2.start();
Thread.sleep(1);
thread1.setFOO(100);
thread2.setFOO(200);
// 예상 -> 0 | 0 | 200 | 100
// 실제 -> 0 | 0 | 200 | 300
// 물론, 우선순위나 멀티 코어 등과 같은 경우 원하는대로 동작 할 수도 있지만 같은 객체를 공유해서 사용한다면 위의 현상처럼 예상치 못한 결과가 발생 할 수도 있으며 이는 프로그램에서 엄청 위험한 부분이다.
// 실제라고 써놓기는 했지만.. 실행될 때의 상황고 선점 순위 등에 의해 정말 예상치 못하게 결과는 나올 것이며 더욱 복잡해질 수록 더 예상할 수 없게 된다.
// 또한 sleep 이 없더라도 FOO 를 변경하는 시점과 FOO 를 print 하는 부분 이 사이에 다른 Thread 가 실행 될 수 있다.
```

## Synchronized Block (동기화 블록)

- 위의 상황에서 공유하는 FOO 를 동기화 한다면 위의 문제는 해결된다.
- 또는 위의 FOO 를 사용하는 부분이 끝날때까지 선점을 해제하지 않으면 된다. 하지만 이 또한 병렬성에서는 소용이 없다.
- 위의 FOO 와 같은 공유 객체를 Read/Write 하는 부분을 멀티 스레드 프로그램에서 하나의 스레드에서만 사용해야 하는 코드 영역을 critical section (임계 영역) 이라 한다.
  - 임계 영역을 지정하는 synchronized 메소드와 동기화 블록을 제공한다.
  - thread 가 synchronized 메소드나 블록에 진입하게 되면 다른 스레드가 임계 코드를 실행하지 못하도록 객체에 잠금을 걸게된다.
  - synchronized 메소드는 인스턴스와 정적 메소드 모두 사용할 수 있다.

```JAVA
public synchronized void method1() {
    // 임계 코드 영역
}
// method1() 를 실행하는 thread 가 존재하면, 다른 thread 에서 method1() 를 만나는 시점에 그 thread 는 처음 method1() 를 만난 thread 가 method1() 를 종료될 떼 까지 기다린다.

public void method2() {
    ...
    syhchronized(obejct) {
        // 임계 코드 블록 영역
    }
    ...
}
// method2() 에 진입하는 모든 thread 는 모두 동시에 실행이 되지만, synchronized 블록에 만난 thread 가 생기면 그 블록에 진입하려는 다른 thread 들은 처음 진입한 thread 가 종료될 때 까지 기다리며, 그 외 thread 들은 계속 동작한다.

public class SharedThread extends Thread {
    private int FOO = 0;
    private int sleepTime = 0;

    @Override
    public void run() {
        synchronized {
            print(FOO);
            Thread.sleep(sleepTime);
            print(FOO);
        }
    }

    public setFOO(int num) {
        this.FOO += num;
    }

    public setSleepTime(int num) {
        this.sleepTime = num;
    }
}

Thread thread1 = new SharedThread();
Thread thread2 = new SharedThread();

thread1.setTime(2000);
thread2.setTime(1000);

thread1.start();
thread2.start();
Thread.sleep(1);
thread1.setFOO(100);
thread2.setFOO(200);
// 0 | 100 | 100 | 300
// thread1 이 2초간 대기이지만, run() 의 전체를 synchronized 블록으로 만들었기에.. 선점을 뺏기지 않고 대기하고 동기 블록이 끝난 시점에 thread2 의 동기 블록이 실행되게 된다.
```

## 스레드 상태

- 스레드 상태
  1. 실행 대기
     - start() 가 호출되자마자 또는 실행 도중 점유를 뺏겨서 다른 스레드가 CPU 를 점유한 경우
  2. 실행
     - 현재 CPU 에 올라가서 동작이 실행되고 있는 상태
  3. 일시 정지
     - 스레드가 실행할 수 없는 상태
     - 일시 정지 상태는 WAITING, TIMED_WAITING, BLOCKED 가 존재한다.
  4. 종료
     - 모든 실행 블록이 끝난 스레드
- 위의 3가지 상태로 동작되며, 스레드는 종료가 될 때 까지 실행 대기와 실행 상태를 반복해서 변경되고 (실행 상태 <-> 실행 대기 상태가 되지 않는 경우도 존재), 여러 스레드들이 번갈아가면서 CPU 의 점유를 가지게 된다.
- 스레드의 상태를 볼수 있는 getState() 메소드가 존재한다.

| 상태      | 열거 상수     | 설명                                                           |
| --------- | ------------- | -------------------------------------------------------------- |
| 객체 생성 | NEW           | 스레드 객체가 생성, 아직 start\(\) 메소드가 호출되지 않은 상태 |
| 실행 대기 | RUNNABLE      | 실행 상태로 언제든지 갈 수 있는 상태                           |
| 일시 정지 | WAITING       | 다른 스레드가 통지할 때까지 기다리는 상태                      |
|           | TIMED_WAITING | 주어진 시간 동안 기다리는 상태                                 |
|           | BLOCKED       | 사용하고자 하는 객체의 락이 풀릴 때까지 기다리는 상태          |
| 종료      | TERMINATED    | 실행을 마친 상태                                               |

## 스레드 제어 상태

- 위의 스레드 상태를 변경할 수 있다.
- 스레드간의 상태를 각 스레드별로 조절은 할 수 있지만, 지금까지 나온 공유 객체나 스레드는 별개로 동작하기 때문에 흐름을 절대적으로 예측하고 설계하기가 매우 어렵다.
- wait(), notify(), notifyAll() 은 Objet 클래스의 메소드이다.
  - 스레드간에 협업을 할 때 사용된다.
  - Object 클래스의 객체이므로 모든 공유 객체에서 호출이 가능하다.
  - 위 메소드들은 동기화 블록 또는 동기화 메소드에서만 사용이 가능하다.

| 메소드                                                                  | 설명                                                                                                                                                                                                      |
| ----------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| interrupt\(\)&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;           | 일시 정지 상태의 스레드에서 InterruptedException 예외를 발생시켜, 예외 처리 코드\(catch\) 에서 실행 대기 상태로 가거나 종료 상태로 갈 수 있도록 한다\.                                                    |
| notify\(\) <br/> notifyAll\(\)                                          | 동기화 블록 내에서 wait() 메소드에 의해 일시 정지 상태에 있는 스레드를 실행 대기 상태로 만든다.                                                                                                           |
| resume\(\)                                                              | ~~suspend() 메소드에 의해 일시 정지 상태에 있는 스레드를 실행 대기 상태로 만든다.~~ <br/> Deprecated 이며 notify(), notifyAll() 를 대신 사용할 수 있다.                                                   |
| sleep\(long millis\) <br/> sleep\(long millis, int nanos\)              | 주어진 시간 동안 스레드를 일시 정지 상태로 만든다. 주어진 시간이 지나면 자동적으로 실행 대기 상태로 된다.                                                                                                 |
| join\(\) <br/> join\(long millis\) <br/> join\(long millis, int nanos\) | join() 메소드를 호출한 스레드는 일시 정지 상태가 된다. 실행 대기 상태로 가려면, join() 메소드를 멤버로 가지는 스레드가 종료되거나, 매개값으로 주어진 시간이 지나야 한다.                                  |
| wait\(\) <br/> wait\(long millis\) <br/> wait\(long millis, int nanos\) | 동기화 블록 내에서 스레드를 일시 정지 상태로 만든다. 매개값으로 주어진 시간이 지나면 자동적으로 실행 대기 상태가 된다. 시간이 주어지지 않으면 notify(), notifiyAll() 을 통해 실행 대기 상태로 갈 수 있다. |
| suspend\(\)                                                             | ~~스레드를 일시 정지 상태로 만든다. resume() 메소드를 호출하면 다시 실행 대기 상태가 된다.~~ Deprecated 이며 wait() 를 대신 사용할 수 있다.                                                               |
| yield\(\)                                                               | 실행 중에 우선순위가 동일한 다른 스레드에게 실행을 양보하고 실행 대기 상태가 된다.                                                                                                                        |
| stop\(\)                                                                | ~~스레드를 즉시 종료시킨다.~~                                                                                                                                                                             |

```JAVA
try {
    Thread.sleep(1000); // 1초 일시 정지
} catch (InterruptedException e) {
    // interrupt() 메소드가 호출되면 실행
}
...
// yeild
thread1.start();
thread2.start();

if (condition) {
    thread1.yeild();
    // thread2 가 실행
}
...
// sleep
public class SumThread extends Thread {
    private long sum = 0;

    public long getSum() {
        return sum;
    }

    public void run() {
        for(int i = 0; i < 100; i++) {
            sum += i;
        }
    }
}

...
// join
SumThread thread3 = new SumThread();
SumThread thread4 = new SumThread();
thread3.start();
thread4.start();

try {
    thread3.join();
} catch (InterruptedException e) {}

print(thread3.getSum()); // 4950;
print(thread4.getSum()); // 0 -> thread 가 현재 코드 블럭과 별개도 동작하면서 thread4 의 값을 프린트 하기 때문에 run() 이 모두 동작 한 뒤 프린트가 되는게 아닌, 초기 값인 0 이 출력된다.

...
// notify | wait
public class SharedMethod {
    public synchronized void methodA() {
        print("메소드 A");
        notify();
        try {
            wait();
        } catch (InterruptedException e) { }
    }

    public synchronized void methodB() {
        print("메소드 B")
        notify();
        try {
            wati();
        } catch (InterruptedException e) { }
    }
}

public class ThreadA extends Thread {
    private SharedMethod sharedMethod;

    public ThreadA(SharedMethod sharedMethod) {
        this.sharedMethod = sharedMethod;
    }

    @Override
    public void run() {
        for(int i = 0; i < 10; i++) {
            sharedMethod.methodA();
        }
    }
}

public class ThreadB extends Thread {
    private SharedMethod sharedMethod;

    public ThreadB(SharedMethod sharedMethod) {
        this.sharedMethod = sharedMethod;
    }

    @Override
    public void run() {
        for(int i = 0; i < 10; i++) {
            sharedMethod.methodB();
        }
    }
}

...
SharedMethod sharedMethod = new SharedMethod();

ThreadA threadA = new ThreadA(sharedMethod);
ThreadB threadB = new ThreadB(sharedMethod);

threadA.start();
threadB.start();
/*
메소드 A
메소드 B
메소드 A
메소드 B
...
*/

// 위의 notify 와 wait 를 이용하여 생산자와 소비자를 구현할 수 있다.
// 소비자는 생산 된 것을 사용해야 하므로, 생산자가 notify() 를 호출하면 소비자가 생산한 것을 소비하고, 모든 소비가 끝나면 자기 자신은 wait 되고 notify 를 호출하면 생산자가 다시 생산을 하는 형태로 구현할 수 있다.

...
threadStop.stop();
// 스레드의 강제 종료. stop() 메소드가 실행 된 순간 스레드는 종료되지만, 스레드 내부에 할당되어 있는 자원이 불안전하게 남아있기 때문에 Deprecated 되어 있다.

threadIntterupted.interrupted();
// threadIntterupted 스레드가 일시정지 되어 있는 상태라면 InterruptedException 이 발생되면서 스레드가 종료된다.
threadInterrupted.isInterrupted(); // return Boolean
// isInterrupted() 메소드를 통해 스레드가 현재 interrupted 가 되어있는지 확인 할 수 있다.
Thread.interrupted(); // return Boolean
// 현재 스레드가 interrupted 여부를 알 수 있다.
```

## Demon thread

- 주 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드.
- 주 스레드가 종료되면, 그 스레드의 데몬 스레드는 강제 종료된다.
- 데몬 스레드를 생성하기 위해서는 start() 메소드를 호출하기 전에 선언 된 스레드에 .setDemon(true); 를 실행하면 실행한 스레드의 데몬 스레드가 되어서 동작된다.

  - start() 되고 setDemon() 을 실행 할 경우 IllegalThreadStateException 이 발생한다.

- isDemon() 메소드를 통해서, 그 스레드가 데몬 스레드인지 확인 할 수 있다.

```JAVA
class TestThread() Thread {
    @Overrid
    public run() {
        try {
            while(true) {
                print("Hello world");
                Thread.sleep(1000);
            }
        } catch ...
    }
}
...
public static void main(String[] args) {
    TestThread thread = new TestThread();
    thread.setDemon(true); // 메인 스레드의 데몬 스레드가 된다.
    print("메인 시작");
    thread.start();
    try {
        Thread.sleep(3500);
    } catch ...
    print("메인 종료");
}

// 메인 시작 -> Hello world -> Hello world -> Hello world -> Hello world -> 메인 종료
```

## Thread Group

- 관련된 스레드를 묶어서 관리할 목적으로 사용할 수 있다.
  1. JVM 이 실행되면 system 스레드 그룹을 만들고, JVM 운영에 필요한 스레드들을 생성해서 system 스레드 그룹에 포함시킨다.
  2. system 하위 스르데 그룹으로 main 을 만들고 메인 스레드들을 main 그룹에 포함시킨다.
  3. 따로 스레드 그룹을 생성하거나 그룹을 명시하지 않으면, 스레드를 만든 그룹에 속하게 된다.
- 스레드 그룹은 getThreadGroup() 으로 얻을 수 있고, group 의 getName() 을 통해 그룹 이름을 얻을 수 있다.
- 스레드의 정적 메소드인 getAllStackTraces() 를 이용하면 프로세스 내에서 실행하는 모든 스레드의 정보를 얻을 수 있다.
- 가비지 컬렉션을 담당하는 Finalizer 는 system 그룹에 속한다.
- 그룹 생성은 ThreadGroup 객체를 생성하고, 생성한 그룹 객체를 스레드를 생성할 때 매개변수로 넘겨주면 그 그룹에 속한 스레드가 생성된다.
  - ThreadGroup(String name)
  - threadGroup(threadGroup parent, String )
  - Thread(threadGroup group, Runnable target);
  - Thread(threadGroup group, Runnable target, String name);
  - Thread(threadGroup group, Runnable target, String name, long stackSize);
  - Thread(threadGroup group, String name);
    - group -> 스레드가 속할 그룹이며, 없다면 생성한 부모의 스레드 그룹에 속하게 된다.
    - target -> Runnable 구현 객체
    - name -> 스레드의 이름
    - stackSize -> JVM 이 스레드에 할당할 stack 크기
- 스레드 그룹에 일괄 interrupt() 가 가능하다. 즉, 그룹에 속한 모든 스레드를 일괄적으로 종료시킬 수 있다.
  - 그룹이 interruptException 에 대한 처리를 하지 않으므로, 각각 스레드들이 interruptException 를 처리 해야 한다.

```JAVA
ThreadGroup group = thread.currentthread().getThreadGroup();
String groupName = group.getName();

Map<Thread, StatckTraceElement[]> threadInfomation = Thread.getAllStackTraces();
// 키는 스레드 객체
// 값은 StackTraceElement[] 배열
```

### ThreadGroup 의 주요 메소드

| 리턴 타입   | 메소드                    | 설명                                                                                                     |
| ----------- | ------------------------- | -------------------------------------------------------------------------------------------------------- |
| int         | activeCount\(\)           | 현재 그룹 및 하위 그룹에서 활동 중인 모드 스레드의 수를 리턴                                             |
| int         | activeGroupCount\(\)      | 현재 그룹에서 활동 중인 모든 하위 그룹의 수를 리턴                                                       |
| void        | checkAccess\(\)           | 현재 스레드가 스레드 그룹을 변경할 권한이 있는지 체크하고, 권한이 없다면 SecurityException 을 발생한다\. |
| void        | destroy\(\)               | 현재 그룹 및 하위 그룹을 모두 삭제한다\. 단, 그룹 내에 포함된 모드 스레드들이 종료 상태가 되어야 한다\.  |
| boolean     | isDestroyed\(\)           | 현재 그룹이 삭제되었는지 여부를 확인                                                                     |
| int         | getMaxPriority\(\)        | 현재 그룹에 포함된 스레드가 가질 수 있는 최대 우선순위를 리턴                                            |
| void        | setMaxPriority\(int pri\) | 현재 그룹에 포함된 스레드가 가질 수 있는 최대 우선순위를 설정                                            |
| String      | getName\(\)               | 현재 그룹의 이름을 리턴                                                                                  |
| ThreadGroup | getParent\(\)             | 현재 그룹의 부모 그룹을 리턴                                                                             |
| boolean     | parentOf\(ThreadGroup g\) | 현재 그룹이 매개값으로 지정한 스레드 그룹의 부모인지 여부를 리턴                                         |
| void        | isDaemon\(\)              | 현재 그룹이 데몬 그룹인지 여부를 리턴                                                                    |
| void        | list\(\)                  | 현재 그룹에 포함된 스레드와 하위 그룹에 대한 정보를 출력                                                 |
| void        | list\(\)                  | 현재 그룹에 포함된 스레드와 하위 그룹에 대한 정보를 출력                                                 |
| void        | interrupt\(\)             | 현재 그룹에 포함된 모든 스레드들을 interrupt                                                             |

## Thread Pool

- 스레드의 수가 많아질 수록, 그 스레드들을 처리하기 위해 CPU 의 사용량은 늘어나고 그로인해 CPU 의 성능을 나빠지게 된다.
- 스레드의 병렬 작업을 조절하기 위해 Thread Pool 이 존재한다.
  - Thread Pool 은 작업 처리에 사용되는 스레드의 제한된 개수만큼 정해 놓고 Queue 에 들어오는 작업들을 하나씩 스레드가 맡아 처리하는 것을 말한다.
- 스레드 풀을 생성하기 위해서 java.util.concurrent 패키지의 ExecutorService 인터페이스와 Executors 클래스를 제공한다.
  - Exeecutors 의 다양한 정적 메소드를 이용하여 ExecutorService 구현 객체를 만들어서 스레드 풀을 만들 수 있다.
  - newCachedThreadPool()
    - 초기 스레드수 0, 코어 스레드 수 0, 최대 스레드 수 Integer.MAX_VALUE
    - 스레드 수 보다 작업 수가 많을 경우, 새 스레드를 생성시커 작업한다.
    - 최대 스레드 수는 Integer.MAX_VALUE 이지만, 운영체제의 성능과 상황에 따라 달라진다.
    - 사용되지 않는 스레드가 60초 이상 존재하면, 그 스레드는 종료가 되고 풀에서 제거된다.
    - ExecutorService executorService = Executors.newCachedThreadPool()
  - new FixedThreadPool(int n Threads)
    - 초기 스레드 수 0, 코어 스레드 수 nThreads, 최대 스레드 수 nThreads
    - 스레드 수 보다 작업 수가 많을 경우, 새 스레드를 생성시커 작업한다.
    - 매개 변수로 준 값이 최대 증가될 수 있는 스레드의 수이다.
    - 스레드가 작업을 처리하지 않고 놀고 있더라도 스레드 개수가 줄지 않는다.
    - ExecutorService executorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors()) - CPU 코어의 수만큼
  - 초기 스레드 수는 ExecutorService 객체가 생성될 떄 기본적으로 생성되는 스레드 수를 말한다.
  - 코어 스레드 수는 스레드가 증가된 후, 사용하지 않는 스레드를 스레드 풀에서 제거할 때 최소한 유지되어야 할 스레드 수
  - new ThreadPoolExecutor() 를 통해서 코어 스레드 수, 최대 스레드 수를 직접 설정 할 수 있다.

```JAVA
ExecutorService threadPool = new ThreadPoolExecutor(
    코어 스레드 수,
    최대 스레드 수,
    idle time (LONG),
    idle 시간 단위 (TimeUnit),
    작업 큐 (new SynchronousQueue<Runnable>()...)
)
```

## 스레드 풀 종료

| 리턴 타입      | 메소드명 \(매개변수\)                           | 설명                                                                                                                                                                                              |
| -------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| void           | shudown\(\)                                     | 현재 처리 중인 작업뿐만 아니라 작업 큐에 대기하고 있는 모든 작업을 처리한 뒤 스레드 풀을 종료 (일반적으로 스레드풀을 종료할 때 사용된다.)                                                         |
| List<Runnable> | shutdownNow\(\)                                 | 현재 작업 처리 중인 스레드를 interrupt 하여 작업 중지를 시도하고 스레드풀을 종료시킨다\. 리턴 값은 작업 큐에 있는 미처리된 작업 \(Runnable\) 목록이다\. (남아있는 작업과 상관없이 강제 종료할 때) |
| boolean        | awaitTermination\(long timeout, TimeUnit unit\) | shutdown\(\) 메소드 호출 이후, 모든 작업 처리를 timeout 시간 내에 완료되면 true, 시간내에 완료하지 못하면 처리를 못한 스레드를 interrupt 하고 false 를 리턴한다\.                                 |

## 작업 생성과 처리 요청

```JAVA
// Runnable 과 Callable 둘다 하나의 작업을 처리할 수 있으나, 차이점은 리턴값이 있는가 없는가 이다.
Runnable task = new Runnable() {
    @Override
    public void run() {
        // 처리할 로직
    }
}

Callable<T> task = new Callable<T>() {
    @Override
    public T call() throws Exception {
        // 처리할 로직
        return T
        // implements Callable<T> 에서 지정한 T 타입을 리턴한다. (제네릭)
    }
}

// 위의 runnable 과 callable 을 스레드 풀이 받아서 run(), call() 을 실행한다.
```

- 작업 처리 요청은 ExecutorService 의 작업 큐에 Runnable, Callable 객체를 넣는 행위를 말한다.
- ExecutorService 의 작업 처리 요청을 위한 메소드

| 리턴 타입                                 | 매소드명 \(매개변수\)                                                                            | 설명                                                                                                                                                                                                  |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| void                                      | execute\(Runnable command\)                                                                      | \- Runnable 을 작업 큐에 저장 <br/> \- 작업 처리 결과를 받지 못함 <br/> \- 작업 처리 도중 예외 발생 시 스레드가 종료되고 해당 스레드는 스레드 풀에서 제거 된다.                                       |
| Future<?> <br/> Future<V> <br/> Future<V> | submit\(Runnable task\) <br/> submit\(Runnable task, V Result\) <br/> submit\(Callable<V> task\) | \- Runnable 또는 Callable 을 작업 큐에 저장 <br/> \- 리턴된 Future 를 통해 작업 처리 결과를 얻을 수 있음 <br/> \- 작업 처리 도중 예외가 발생해도 스레드는 종료되지 않고 다음 작업을 위해 재사용 된다. |

- 스레드의 생성 오버헤더를 줄이기 위해 submit() 을 사용하는 것이 좋다.

## 작업 완료 통보

### 블로킹 방식

- submit() 메소드는 매개값으로 준 Runnable, Callable 작업 스레드 풀의 작업 큐에 저장하고 즉시 Future 객체를 리턴한다.
- Future 객체는 작업 결과가 아니고 작업이 완료될 때까지 블로킹 되었다가 최종 결과를 얻는데 사용된다.
  - 블로킹 되었다가 (지연 되었다가) 결과를 얻기 때문에 지연 완료 객체 (pending completion object) 라고 한다.
- Future 의 get() 을 통해 작업이 완료된 시점에 결과를 얻어서 사용 할 수 있다.
- 블로킹 방식이기 때문에, get() 을 만나는 시점에 future 객체의 결과를 처리할 때 까지 어떤 이벤트도 처리할 수 없다.
  - get() 메소드를 호출하는 스레드는 새로운 스레드이거나 스레드풀의 또 다른 스레드가 되어야 한다.

| 리턴 타입                  | 매소드명 \(매개변수\)              | 설명                                                                                               |
| -------------------------- | ---------------------------------- | -------------------------------------------------------------------------------------------------- |
| V                          | get\(\)                            | 작업이 완료될 때까지 블로킹되었다가 처리 결과 V 를 리턴                                            |
| V &emsp;&emsp;&emsp;&emsp; | get\(long timeout, TimeUnit unit\) | timeout 시간 이전에 작업이 완료되면 결과 V 를 리턴하지만, 완료되지 않으면 TimeoutException 이 발생 |

- V 는 sumbit(Runnable task, V result) 의 result 라는 V 타입이거나 submit(Callable<V> task) 의 Callable 타입 파라미터 V 타입이다.

- Future 의 get() 메소드 리턴

| 메소드                         | 작업 처리 완료 후 리턴 타입 | 작업 처리 도중 예외 발생      |
| ------------------------------ | --------------------------- | ----------------------------- |
| submit\(Runnable task\)        | future\.get\(\) \-> null    | future\.get\(\) \-> 예외 발생 |
| submit\(Callable<String> task> | future\.get\(\) \-> String  | future\.get\(\) \-> 예외 발생 |
| submit\(Callable<String> task> | future\.get\(\) \-> String  | future\.get\(\) \-> 예외 발생 |

- Future 의 get 이외의 다른 메소드

| 리턴 타입 | 매소드명 \(매개변수\)                 | 설명                                                                                                                                                                                                                          |
| --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| boolean   | cancel\(boolean mayInterruptRunning\) | 작업 처리가 진행 중일 경우 취소 <br/> 작업이 시작되기 전이면 바로 취소 <br/> 작업이 실행 도중이면, mayInterruptRunning 이 true 일 경우 interrupt <br/> 작업이 완료되었거나, 어떠한 이유로 취소할 수 없으면 false 를 리턴한다. |
| boolean   | isCancelled\(\)                       | 작업 처리가 완료되었는지 여부                                                                                                                                                                                                 |
| boolean   | isDone\(\)                            | 작업 처리가 완료되었는지 여부 <br/> 정상적, 예외, 취소 등 어떤 이유든 작업이 완료되면 true 를 리턴한다.                                                                                                                       |

### 리턴값이 없는 작업 통보 방식

- 작업 처리 완료 결과값이 없더라도 submit(Runnable task) 의 결과값은 Future 인데, 작업 처리 완료의 값은 없지만 스레드의 작업 처리에 대한 성공, 예외 등의 정보를 가지고 있다.
  - 이런 경우 정상 처리가 되어 get() 을 하면 null 이 리턴된다.
  - 처리도중 interrupt 가 되면 InterruptException 이, 작업 도중 예외가 발생하면 ExecutionException 이 발생한다.

### 리턴값이 있는 작업 통보 방식

- 스레드의 작업 완료 후에 애플리케이션이 처리 결과를 얻어야 한다면, 작업 객체를 Callable 객체로 생성하면 된다.
  - Callable 의 리턴 타입은 제네릭 T 이고, 원하는 타입의 리턴 값으로 지정해주면 된다.
- Callable 객체를 ExecutorService 의 submit() 매개변수를 줄 경우, 작업 풀에 바로 넘겨준 객체를 등록하고 Future<T> 를 리턴한다.

```JAVA
Callable<Integer or String or Double ......> task = new Callable<앞에 선언한 타입과 같은 타입>() {
    @Override
    public 위에 선언한 타입과 같은 타입 call() throws Exception {
        ....
        return 위에 선언한 같은 타입의 리턴 값;
    }
};
```

### 작업 처리 결과를 외부 객체에 저장

- 스레드가 처리한 결과를 외부 객체를 저장해야 하는 경우, 외부 Result 객체에 작업 결과를 저장할 수 있다.
  - 대개 Result 객체는 공유 객체가 되어, 두개 이상의 스레드 작업을 취합할 목적으로 이용된다.
- sumbit(Runnable task, V result) 의 V 타입의 result 값에 task 의 결과가 저장되고, result 는 공유되어 다른 스레드의 매개변수로 넘겨지면 result 에 그 스레드들의 결과값이 공유되어 저장된다.

```JAVA
class Task implements Runnable {
    Result result;
    Task(Result result) { this.result = result }
    // 스레드의 결과 저장을 위해 result 객체를 사용해야 하므로 생성자를 통해 result 객체를 주입받도록 하자.
    @Override .....
}

...

Result result = ..;
Runnable task = new Task(result);
Future<Result> future = executorService.submit(task, result);
result = future.get();
```

### 작업 완료 순으로 통보

- 작업의 양, 스레드 스케줄링에 따라 작업이 완료되는 순서는 달라진다.
  - FIFO 가 보장될 수 없다.
- 처리 결과도 순차적으로 처리할 필요가 없다면, 완료된 것부터 결과를 얻어서 처리하면 된다.
  - 스레드풀에서 작업 처리가 완료된 것만 통보받는 CompletionService 를 이용하면 된다.
  - CompletionService 는 처리 완료된 작업을 가져오는 poll() 과 take() 메소드가 존재한다.
  - CompletionService 의 구현 클래스는 ExecutorCompletionService<V> 이다.
    - 객체를 생성할 때 생성자 매개값으로 ExecutorService 를 제공한다.

| 리턴 타입 | 매소드명 \(매개변수\)               | 설명                                                                                   |
| --------- | ----------------------------------- | -------------------------------------------------------------------------------------- |
| Future<V> | poll\(\)                            | 완료된 작업의 Future 를 가져온다\. <br/> 완료된 작업이 없다면 즉시 null을 리턴함\.     |
| Future<V> | poll\(long timeout, TimeUnit unit\) | 완료된 작업의 Future 를 가져온다\. <br/> 완료된 작업이 없다면 timeout 까지 블록킹 함\. |
| Future<V> | submit\(Callable<V> task>           | 스레드풀에 Callable 작업 처리 요청                                                     |
| Future<V> | submit\(Callable<V> task>           | 스레드풀에 Callable 작업 처리 요청                                                     |
| Future<V> | submit\(Runnable task, V result\)   | 스레드풀에 Runnable 작업 처리 요청                                                     |

```JAVA
ExecutorService executorService = Executors.newFixedThreadPool(
    Runntime.getRuntime().availableProcessors()
);

CompletionService<V> completionService = new ExecutorCompletionService<V>(
    executorService
)

...

completionService.submit(callableTask);
completionService.submit(runnableTask, resultObject);

executorService.submit(new Runnable() {
    @Override
    public void run() {
        while(ture) {
            try {
                Future<Integer> future = completionService.task();
                ....
                // 처리 된 결과인 future 를 이용하여 스레드 결과 처리
                // take() 는 작업이 완료된 객체가 있을 때 까지 블록킹 되므로
                // 따로 스레드를 생성하여 작업이 처리되는대로 결과를 받아서
                // 처리하는 방식이 좋다.
                // 이 예제에서 callableTask -> runnableTask 의 순으로
                // 결과가 처리된다는 보장이 없다.
            } catch (Exception e) { }
        }
    }
})
```

### 콜백 방식의 작업 완료 통보

- 콜백이란 애플리케이션 스레드에게 작업 처리를 요청한 후, 스레드가 작업을 완료하면 특정 메소드를 자동으로 실행하는 기법을 말한다.
- 자동으로 실행되는 메소드를 콜백 메소드라 한다.
- 콜백 방식은 블로킹 방식과 다르게 요청 처리를 기다릴 필요 없이, 결과가 생기면 콜백 메소드를 통해 실행하게 되므로 결과를 알아서 처리하게 된다.
- ExecutorService 는 콜백을 위한 별도의 기능을 제공하지 않는다.
  - 하지만, Runnable 구현 클래스를 작성할 때 콜백 기능을 구현할 수 있다.
  - 콜백 메소드를 가진 클래스를 직접 정의할 수도 있고, java.nio.channels.CompletionHandler 를 이용할 수도 있다.
  - NIO 패키지에 포함되어 있는 인터페이스인데, 이는 비동기 통신에서 콜백 객체를 만들때 사용된다.

```JAVA
CompletionHandler<V, A> callback = new CompletionHandler<V, A>() {
    @Override
    public void completed(V result, A attachment) {}
    // 처리가 정상적일 경우 호출되는 콜백 메소드

    @Override
    public void failed(Throwable exc, A attachment) {}
    // 작업 처리 도중 예외가 발생하였을 경우 호출되는 콜백 메소드
}

// A 는 첨부값의 타입인데, 콜백 메소드에 결과값 이외에 추가적으로 전달하는 객체이다.

...

Runnable task = new Runnable() {
    @Override
    public void run() {
        try {
            ...
            V result = ...;
            callback.completed(result, null);
        } catch(Exception e) {
            callback.failed(e, null);
        }
    }
}
```
