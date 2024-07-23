## 1.C++中的多线程

![image-20240717150251700](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240717150251700.png)

## 2.创建一个线程

使用std::thread类
这是C++11及以后版本推荐的方式。你可以直接创建一个std:thread对象来启动一个新的线程，并传递一个可调用对象(如函数、函数对象、Lambda表达式等)作为线程的执行体。



## 3.线程的可调用对象

在C++中，线程的可调用对象(callable object)可以是多种类型，包括但不限于:函数指针、成员函数指针、lambda表达式、函数对象(也称为仿函数或functor)以及绑定对象(通过std:bind创建)。以下是一些示例代码:

### 3.1使用函数指针

```
#include <iostream>
#include <thread>

void threadFunction(int a) {
    std::cout<<"子线程"<<a<<std::endl; 
}
int main() {
	
    std::thread t(threadFunction,10);
	
    if (t.joinable()) {
        // 等待线程完成
        t.join();
    }

    // 此时 t 不再是 joinable 的
    if (t.joinable()) {
        std::cout << "Thread is still joinable." << std::endl;
    } else {
        std::cout << "Thread is not joinable." << std::endl;
    }
	std::cout<<"main线程"<<std::endl;
    return 0;
}
```

### 3.2使用成员函数指针

```
#include <iostream>
#include <thread>

class MyClass {
	public:
	void memberFunctionTask(){
	std::cout<<"Member function task executed.\n";
}
};
int main(){
	
	MyClass obj;
	std::thread t(&MyClass::memberFunctionTask,&obj);//注意需要传递对象指针
	t.join();
	return 0;
}
```

### 3.3使用Lambda表达式

```
#include <iostream>
#include <thread>


int main(){
	
	std::thread t([](){
		std::cout<<"Lambda task executed.\n";
	});
	t.join();
	return 0;
}

```

### 3.4函数对象(也称为仿函数或functor)

```
#include <iostream>
#include <thread>


struct Functor {
	void operator()(){
		std::cout<<"Functor task executed.\n";
	}
};
int main(){
	Functor f;
	std::thread t(f);
	t.join();
	return 0;
}
```

### 3.5绑定对象(通过std:bind创建)

```
#include <iostream>
#include <thread>
#include <functional>

void functionWithArgs(int a,int b){
	std::cout<<"Function with args:"<<a<<","<<b<<".\n";
}
int main(){
	auto boundFunc =std::bind(functionWithArgs,1,2);
	std::thread t(boundFunc);
	t.join();
	return 0;
}
```



## 4.如何传参数

在C++多线程编程中，当你想要在一个新的线程中执行一个可调函数(通常通过std:thread或类似的机制)，并且这个函数需要参数时，你可以直接将参数传递给std:.thread的构造函数。这里有两种主要的传递方式:通过值(by value)或通过引用(by reference)

### 4.1按值传递

当你通过值传递参数时，
你实际上是在传递参数的副本。
这意味着原始数据和线程中使用的数据是两个独立的实体

![image-20240717155047477](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240717155047477.png)

### 4.2 按引用传递

当你通过引用传递参数时，你实际上是在传递对原始数据的引用，而不是数据的副本。这意味着原始数据和线程中使用的数据是同一个实体。但是，这需要小心处理，因为如果你在一个线程中修改了数据，这些修改将在所有引用该数据的线程中可见。

```
#include <iostream>
#include <thread>
void modify_and_print(int& a, int& b){
	a += 5;
	b += 10;
	std::cout<<"Modified values:"<<a<<" "<< b<< std::endl;
	
}
int main(){
	int x=5;
	int y= 10;
	std::thread t(modify_and_print, std::ref(x),std::ref(y));
	t.join();
	std::cout<<"Values in main thread: "<< x<< ","<<y<< std::endl;
	return 0;
}
```

在这个例子中，我们使用了std:.ref来包装x和y的引用，并将它们传递给新线程中的modify and print函数。由于我们
修改了这些值，
所以原始变量x和y在main线程中的值也被改变。

### 4.3注意事项

- **线程安全性:**当通过引用传递数据时，必须确保对这些数据的访问是线程安全的。否则，你可能会遇到数据竞争和其他并发问题。
- **资源管理:**确保你正确地管理了所有在新线程中创建或使用的资源。特别是，如果新线程使用了动态分配的内存或其他资源，你需要确保这些资源在不再需要时被正确释放。
- **异常处理:**新线程中抛出的异常不会自动传播到创建该线程的线程。因此，你需要确保在新线程中正确地处理所有可能的异常。

## 5. join与的detach的区别

### 1. join方法:

- 用当一个线程调用join方法时，它会阻塞当前线程(调用join的线程)，直到被调用的线程(即join的参数所指定的线程)执行完成。
- 这可以确保在主线程中，子线程执行完毕之前不会结束主线程的执行
- 使用join可以确保线程的资源被正确地回收和清理，因为当ioin返回时，线程对象所代表的线程已经完成了执行。
- 如果在子线程未执行完毕的情况下尝试销毁其对应的std:.thread对象，而该对象又未被join或detach，则会导致程序终止。

### 2. detach方法:

- 当调用detach方法时，线程将与其所属的线程(即调用detach的线程)分离，并在后台独立运行。
- 一旦线程被分离，它将不再受到主线程的控制，主线程也不再需要调用join来等待它的结束。
- 分离的线程会自动回收其资源，当线程执行完毕后，其资源会被系统回收。
- 需要注意的是，一旦线程被分离，就无法再对其进行join操作，因为此时线程已经脱离了主线程的控制。

join和detach的主要区别在于它们对线程执行完成后的处理方式不同。join会阻塞当前线程并等待子线程执行完成而detach则会使线程在后台独立运行并自动回收资源。

## 6.一个线程包含什么

一个线程通常包含以下组成部分:

1. **线程ID:**每个线程在系统中都有一个唯一的标识符，用于区分不同的线程。
2. **线程栈(Thread stack):**每个线程都有自己私有的栈空间，用于存储局部变量、函数调用时的参数和返回地址等信息。线程栈在创建线程时分配，并在线程结束时释放。
3. **线程状态:**线程的状态描述了线程当前的生命周期阶段，例如新建(New)、就绪(Ready)、运行(Running)、阳塞(Blocked)和终止(Terminated)等。
4. **线程上下文(Thread Context):**线程上下文包含了线程执行时所需的所有信息，如CPU寄存器的内容、程序计数器(PC)的值、栈指针、信号掩码等。当线程被切换时，线程上下文会被保存，以便在之后恢复执行时能够继续执行。
5. **线程函数(Thread Function):**线程函数是线程执行的具体逻辑，它包含了线程需要执行的代码。线程函数通常是由用户定义的，并在创建线程时作为参数传递给线程创建函数。
6. **线程优先级:**操作系统可以根据线程优先级来决定调度哪个线程执行。优先级较高的线程会获得更多的处理器时间。
7. **线程属性:**线程属性用于设置线程的一些特性，如栈大小、安全属性等，这些属性可以在创建线程时设置，也可以通过其他线程管理函数进行修改。
8. **线程同步原语:**为了协调多个线程的执行，C++提供了多种线程同步原语，如互斥锁(Mutex)、条件变量8.(Condition Variable)、信号量(Semaphore)等。这些原语可以帮助线程在访问共享资源时避免冲突和竞态条件。

## 7.this thread

td:this thread 是C++ 标准库中的一个命名空间，它提供了与当前线程相关的函数和工具。在多线程编程中std::this thread 命名空间下的函数允许我们获取和操作当前线程的特定属性，如获取线程ID、使当前线程休眠等。

以下是一些 std::this thread 命名空间下常用的函数及其使用方法:

### 1.**获取当前线程的ID**

使用 std:this thread:get id() 函数可以获取当前线程的ID。这个ID是一个 std::thread::id 类型的对象，可以用于比较两个线程是否是同一个线程。

```
#include <iostream>
#include <thread>
void modify_and_print(int& a, int& b){
	a = 5;
	b = 10;
	std::cout<<std::this_thread::get_id()<<" "<<"Modified values:"<<a<<" "<< b<< std::endl;
	
}
int main(){
	int x=5;
	int y= 10;
	std::thread t(modify_and_print, std::ref(x),std::ref(y));
	t.join();
	std::cout<<std::this_thread::get_id()<<"Values in main thread: "<< x<< ","<<y<< std::endl;
	return 0;
}
```

### 2.使当前线程休眠

std:：this thread:：sleep for( )和 std::this thread::sleep until() 函数可以使当前线程休眠一段时间。前者使线程休眠指定的时间长度(如毫秒、秒等)，后者则使线程休眠到指定的时间点。

## 8.线程同步与mutex

- **线程同步**(Thread Synchronization)是多线程编程中的一个重要概念，它指的是**通过一定的机制来控制多个线程之间的执行顺序，以确保它们能够正确地访问和修改共享资源**，从而避免数据竞争和不一致性的问题。
- 在多线程环境中，多个线程可能同时访问和修改共享资源(如变量、数据结构或文件等)。如果没有适当的同步机制，这些线程可能会以不可预测的顺序执行，导致数据竞争、脏读、脏写或其他不可预期的行为。线程同步的目标就是确保线程之间的有序执行，以维护数据的一致性和完整性。

<img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240717212948137.png" alt="image-20240717212948137" style="zoom:60%;" />

<img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240717213123640.png" alt="image-20240717213123640" style="zoom:60%;" />

由于没有线程互斥的存在，针对同一个变量发生多个线程的瞬息同写最终导致了数据的混乱，谁都不能拿到正确的数据

### 1.线程同步机制

C++中提供了多种线程同步机制，常用的方法包括:

1. **互斥锁(Mutex):**互斥锁是最常用的线程同步机制之一。当一个线程想要访问共享资源时，它首先会尝试获取与该资源关联的互斥锁。如果锁已经被其他线程持有，则该线程将被阻塞，直到锁被释放。这样可以确保在任何时候只有一个线程能够访问共享资源。
2. **条件变量(Condition Variable):**条件变量用于使线程在满足某个条件之前等待。它通常与互斥锁一起使2用，以便在等待条件成立时释放锁，并在条件成立时重新获取锁。这允许线程在等待期间不占用锁，从而提高并发性能。
3. **信号量(Semaphore):**信号量是一种通用的线程同步机制，它允许多个线程同时访问共享资源，但限制同2时访问的线程数量。信号量内部维护一个计数器，用于表示可用资源的数量。当线程需要访问资源时，它会尝试减少计数器的值;当线程释放资源时，它会增加计数器的值。当计数器的值小于零时，尝试获取资源的线程将被阻塞。
4. **原子操作(Atomic Operations):**原子操作是不可中断的操作，即在执行过程中不会被其他线程打断。C++11及以后的版本提供了<atomic>头文件，其中包含了一系列原子操作的函数和类。这些原子操作可以用于安全地更新共享数掴、而无需德需互斥锁等同步机制。

### 2.互斥锁mutex

- **互斥(Mutex)**是一种同步机制，用于保护共享资源，防止多个线程同时访问和修改同一资源，从而引发数据竞争(data race)和不一致性。
- 当一个线程想要访问某个共享资源时，它首先会尝试获取与该资源关联的互斥锁(mutex)。如果互斥锁已经被其他线程持有(即被锁定)，则该线程将被阻塞，直到互斥锁被释放(即被解锁)。一旦线程成功获取到互斥锁，它就可以安全地访问共享资源，并在访问完成后释放互斥锁，以便其他线程可以获取该锁并访问资源。

互斥锁通常具有以下几个特性:
**1.互斥性:**任意时刻只有一个线程可以持有某个互斥锁。

**2.原子性:**对互斥锁的获取和释放操作是原子的，即在执行这些操作时不会被其他线程打断。

**3.可重入性:**某些互斥锁类型(如递归锁)允许同一线程多次获取同一个锁，但通常不建议这样做，因为它会增加死锁的风险。

**4.非阻塞性:**虽然互斥锁本身是一种阻塞性同步机制，但某些高级实现(如尝试锁)允许线程在无法立即获取锁时继续执行其他任务，而不是被阻塞。

```
#include <iostream>
#include <thread>

//共享变量，没有使用互斥锁或原子操作
int counter =0;

//线程函数，对counter进行递增操作
void increment_counter(int times){
	for(int i=0;i< times; ++i)// 这是一个数据竞争，因为多个线程可能同时执行这行代码
	counter++;
}

int main(){
	// 创建两个线程，每个线程对counter递增10000次
	std::thread t1(increment_counter, 100000);
	std::thread t2(increment_counter, 100000);
	//等待两个线程完成
	t1.join();
	t2.join();
	// 输出结果，可能不是20000
	std::cout<<"最终的结果:"<< counter << std::endl;
	counter=0;
	return 0;
}
```

使用互斥锁

```
#include <iostream>
#include <thread>
#include <mutex>

// 共享变量，使用互斥锁保护
int counter = 0;
std::mutex counter_mutex; // 互斥锁

// 线程函数，对counter进行递增操作
void increment_counter(int times) {
    for (int i = 0; i < times; ++i) {
        // 加锁，确保对counter的访问是线程安全的
        counter_mutex.lock();
        counter++;
        counter_mutex.unlock(); // 解锁
    }
}

int main() {
    // 创建两个线程，每个线程对counter递增10000次
    std::thread t1(increment_counter, 100000);
    std::thread t2(increment_counter, 100000);
    // 等待两个线程完成
    t1.join();
    t2.join();
    // 输出结果，应该是20000
    std::cout << "最终的结果: " << counter << std::endl;
    counter = 0; // 重置counter
    return 0;
}

```

### 3.注意事项

**1.死锁:**
如果线程在持有互斥量的情况下调用了一个阻塞操作(如另一个互斥量的lock())，并且这个阻塞操作永远不会完成(因为其他线程持有它需要的资源)，那么就会发生死锁。避免死锁的一种方法是始终按照相同的顺序锁定互斥量，或者使用更高级的同步原语，**如std::lock quard或std:.unique lock**，它们可以自动管理锁的获取和释放。
2.**异常安全**:
如果在锁定互斥量后抛出异常，那么必须确保互斥量被正确解锁。**使用std:lock guard或std:.unique lock**可以自动处理这种情况，因为它们在析构时会释放锁。
像下面的例子:

```
#include <iostream>
#include <thread>
#include <mutex>

// 共享变量，使用互斥锁保护
int counter = 0;
std::mutex counter_mutex; // 互斥锁

// 线程函数，对counter进行递增操作
void increment_counter(int times) {
    for (int i = 0; i < times; ++i) {
        // 使用std::lock_guard自动管理锁的获取和释放
        std::lock_guard<std::mutex> guard(counter_mutex);
        counter++;
    }
}

int main() {
    // 创建两个线程，每个线程对counter递增10000次
    std::thread t1(increment_counter, 100000);
    std::thread t2(increment_counter, 100000);
    // 等待两个线程完成
    t1.join();
    t2.join();
    // 输出结果，应该是20000
    std::cout << "最终的结果: " << counter << std::endl;
    counter = 0; // 重置counter
    return 0;
}
```

```
#include <iostream>
#include <thread>
#include <mutex>
#include <functional>

// 共享变量，使用互斥锁保护
int counter = 0;
std::mutex counter_mutex; // 互斥锁

// 线程函数，对counter进行递增操作
void increment_counter(int times) {
    for (int i = 0; i < times; ++i) {
        // 使用std::unique_lock可以手动释放锁
        std::unique_lock<std::mutex> guard(counter_mutex);
        counter++;
    }
}

int main() {
    // 创建两个线程，每个线程对counter递增10000次
    std::thread t1(increment_counter, 100000);
    std::thread t2(increment_counter, 100000);
    // 等待两个线程完成
    t1.join();
    t2.join();
    // 输出结果，应该是20000
    std::cout << "最终的结果: " << counter << std::endl;
    counter = 0; // 重置counter
    return 0;
}
```

**3.不要手动解锁未锁定的互斥量:**
在调用unlock0)之前，必须确保互斥量已经被lock()锁定。否则，行为是未定义的。

**4.不要多次锁定同一互斥量***:
对于非递归互斥量(如std:mutex)不要在同一线程中多次锁定它。这会导致未定义的行为。如果需要递归锁定，请使用std::recursive mutex。
**5.使用RAII管理锁:**
使用RAI(资源获取即初始化)原则来管理锁的生命周期，通过std:lock quard或std:.unique lock来确保锁在不需要时自动释放。
**6.避免长时间持有锁:**
尽量缩短持有锁的时间，以减少线程之间的争用，提高程序的并发性能。

**7.考虑使用更高级的同步原语:**
除了std.:mutex之外，C++标准库还提供了其他更高级的同步原语，如条件变量(std:condition variable)、读写锁(std::shared mutex)等，它们可以在特定场景下提供更高效的同步机制。



### 4.mutex的4种类型

在C++中，特别是从C++11开始，std:mutex(互斥量)及其相关类型提供了一系列用于同步多线程访问共享资源的机制。以下是std::mutex的四种主要类型及其详细解释:

1. **std::mutex:**

   这是最基本的互斥量类型

   它不允许递归锁定，即同一线程不能多次锁定同一个std::mutex。如果尝试这样做，程序的行为将是未定义的，通常会导致死锁。
   它提供了基本的锁定(lock0)和解锁(unlock0))操作。

   当一个线程锁定了一个std:mutex时，任何其他尝试锁定该互斥量的线程都将被阻塞，直到原始线程调用unlock()释放它。

2. **std::recursive_mutex:**
   这是一个递归(或可重入)互斥量。
   与std:mutex不同，它允许同一线程多次锁定同一个互斥量。这可以用于需要递归访问受保护资源的场景。
   线程在每次锁定时都需要对应地解锁，以确保正确的同步。

   如果线程没有正确匹配其锁定和解锁操作(即解锁次数少于锁定次数)，则其他线程仍然会被阻塞。

3. **std::timed_mutex:**
   这是一个带时限的互斥量。
   除了提供基本的锁定和解锁操作外，它还允许线程尝试在一定时间内锁定互斥量。
   如果在指定的时间内无法获取锁，try_lock_for()或try_lock_until()函数将返回失败，而线程则不会被阻塞。
   这对于实现有超时机制的资源访问非常有用。
4. **std::recursive_timed_mutex:**
   这是一个递归且带时限的互斥量。
   它结合了std::recursive mutex和std::timed mutex的特性,
   它允许同一线程多次锁定同一个互斥量，并提供了带时限的锁定尝试功能。
   这使得线程在需要递归访问资源且希望在一定时间内获得锁的场景中更加灵活。在使用这些互斥量类型时，需要注意正确地管理锁定和解锁操作，以避免死锁和其他同步问题。同时，根据具体的应用场景和需求选择合适的互斥量类型也是非常重要的。

### 5.unique_lock

**1.为什么会有unique_lock?**
因为mutex 在管理方面有瑕疵，因此出现了一个互斥量封装器lock_guard来智能地管理mutex。而lock_guard只是简单的管理，功能比较弱，有瑕疵。因此需要搞出来一个功能更强大的东西出来。而这个东西就是unique_lock .
本质上来说lock_guard 和unique_lock都是为了更好地使用各种锁而诞生的。但是unique_lock更为灵活，功能更强大，可做的操作比较多。
unique_lock 有些时候也被称为:“灵活锁”

**2.lock_guard和unique_lock的区别**

`lock_guard`和`unique_lock`都是C++11中提供的用于线程同步的互斥量包装器，它们都可以在作用域内自动锁定和解锁互斥量，以防止死锁的发生。但是，它们之间也有一些显著的区别：

1. **灵活性**：
   - `lock_guard`是一种轻量级的互斥量管理工具，它在构造时自动锁定互斥量，并在析构时自动解锁互斥量。它不支持手动解锁和重新锁定。
   - `unique_lock`提供了更多的灵活性，它允许手动解锁和重新锁定互斥量。此外，它还提供了`try_lock`和`defer_lock`等构造选项，允许更精细的锁控制。
2. **性能**：
   - 由于`lock_guard`的设计更为简单，通常它的性能开销比`unique_lock`要小。
3. **条件变量**：
   - `unique_lock`可以与条件变量一起使用，它提供了`wait`、`notify_one`和`notify_all`成员函数，这些在`lock_guard`中是不存在的。
4. **所有权转移**：
   - `unique_lock`对象可以通过`std::move`操作转移所有权，而`lock_guard`则不支持。
5. **构造函数选项**：
   - `unique_lock`提供了多个构造函数重载，允许在创建时选择是否立即锁定互斥量，而`lock_guard`总是立即锁定。
6. **异常安全**：
   - 两者都能提供基本的异常安全保证。但是，由于`unique_lock`提供了更多的控制，它可以在异常发生时更好地处理锁的状态。

以下是一个简单的例子，展示了`lock_guard`和`unique_lock`的使用：

```
#include <iostream>
#include <mutex>
#include <thread>

std::mutex mtx; // 全局互斥量

void func_lock_guard() {
    std::lock_guard<std::mutex> guard(mtx);
    // 在这里，mtx被锁定
    // ...
}

void func_unique_lock() {
    std::unique_lock<std::mutex> ul(mtx, std::defer_lock);
    // mtx尚未锁定
    ul.lock(); // 手动锁定
    // ...
    ul.unlock(); // 手动解锁
    // ...
    ul.lock(); // 可以再次锁定
}

int main() {
    std::thread t1(func_lock_guard);
    std::thread t2(func_unique_lock);

    t1.join();
    t2.join();

    return 0;
}
```

在实际应用中，如果不需要`unique_lock`提供的额外功能，建议使用`lock_guard`，因为它更轻量，更简洁。如果需要更灵活的锁管理，则应该使用`unique_lock`。

unique lock的构造。

```
std::mutex mtx;

std::unique_lock<std::mutex> lk1(mtx);//自动锁定
std::unique_lock<std::mutex>lk2(mtx，std::defer_lock);//延迟锁定 不要自
std::unique_lock<std::mutex>lk3(mtx，std::try_to_lock);// 尝试锁定
std::unique_lock<std::mutex>lk4(mtx，std::adopt_lock);// 接受已锁定的 
```

在C++中，`std::unique_lock` 提供了多种构造函数重载，以适应不同的锁定策略。下面是对您提供的每种构造方式的简要说明：

1. `std::unique_lock<std::mutex> lk1(mtx);`
   - 这个构造函数会自动锁定互斥量`mtx`。`lk1`会在构造时尝试锁定互斥量，并在析构时自动解锁。
2. `std::unique_lock<std::mutex> lk2(mtx, std::defer_lock);`
   - 使用`std::defer_lock`标志，这个构造函数不会立即锁定互斥量。您需要在后续的代码中显式地调用`lk2.lock()`来锁定互斥量。
   
   - ```
     #include <iostream>
     #include <mutex>
     #include <thread>
     
     std::mutex mtx;
     int counter=0;
     void example_defer_lock(){
     	std::unique_lock<std::mutex> lock(mtx,std::defer_lock);// 不会立即锁定
     	//...执
     	lock.lock();//住需要的显式锁定
     	for(int i=0;i<10000;i++)
     	counter++;
     	
     	std::cout<<"Locked with defer lock"<< std::endl;
     	std::cout<<"counter: "<<counter<< std::endl;
     	lock.unlock();//显式解锁
     }
     int	main()
     {
     	std::thread t1(example_defer_lock);
     	std::thread t2(example_defer_lock);
     	t1.join();
     	t2.join();
     	return 0;
     }
     
     
     ```
   
3. `std::unique_lock<std::mutex> lk3(mtx, std::try_to_lock);`
   
   - 使用`std::try_to_lock`标志，这个构造函数会尝试锁定互斥量，但如果互斥量当前不可用（例如，已经被其他线程锁定），它不会阻塞，而是立即返回。您可以通过`lk3.owns_lock()`来检查是否成功获得了锁。
   
   - ```
     #include <iostream>
     #include <mutex>
     #include <thread>
     
     std::mutex mtx;
     
     void example_try_to_lock(){
     	std::unique_lock<std::mutex> lock(mtx,std::try_to_lock);//尝试锁定 
     	if(lock.owns_lock())
     	std::cout<<"Locked with try_to_lock"<< std::endl;
     	else
     	std::cout<<"Failed to lock with try_to_lock"<< std::endl;
     }
     int	main()
     {
     	std::thread t1(example_try_to_lock);
     	std::thread t2(example_try_to_lock);
     	t1.join();
     	t2.join();
     	return 0;
     }
     
     
     ```
4. `std::unique_lock<std::mutex> lk4(mtx, std::adopt_lock);`
   - 使用`std::adopt_lock`标志，这个构造函数假设调用者已经拥有了互斥量的所有权。因此，在创建`lk4`之前，您应该已经通过调用`mtx.lock()`或类似操作锁定了互斥量。`std::adopt_lock`不会尝试锁定互斥量，只是接管已存在的锁。
   
   - ```
     #include <iostream>
     #include <mutex>
     #include <thread>
     
     std::mutex mtx;
     
     void example_adopt_lock(){
     	mtx.lock();
     	std::unique_lock<std::mutex> lock(mtx,std::adopt_lock);//尝试锁定 
     
     
     	std::cout<<"Locked with adopt_lock"<< std::endl;
     	
     }
     int	main()
     {
     	std::thread t1(example_adopt_lock);
     
     	t1.join();
     
     	return 0;
     }
     
     
     ```
   
     

**std::unique_lock 提供了一些成员函数，用于管理锁定状态**

1. lock():锁定关联的 mutex。
2. unlock():解锁关联的 mutex。
3. try_lock():尝试锁定 mutex，如果锁定成功，返回 true;否则返回 false。
4. owns_lock():返回一个布尔值，指示 unique lock 是否拥有 mutex 的所有权。 

### 6.lock_guard

**1.RAll(Resource Acquisition ls lnitialization):**
std:.lock guard 在构造时锁定 mutex，在析构时解锁 mutex。这样可以确保在任何情况下(包括异常) mutex 都能被正确解锁。
**2.简单易用:**
使用 std:.lock guard 可以简化代码，只需在需要锁定的代码块内创建一个 std:.lock guard 对象即可无需手动调用 lock0) 和 unlock0。
**3.异常安全:**
由于 std:.lock quard 的析构函数自动解锁 mutex，所以即使在锁定期间抛出异常，mutex 也能被正确解锁。
**4.不可复制和不可移动:**
std:lock guard 是不可复制和不可移动的。这确保了 mutex 锁的独占性，防止锁被意外复制或移动导致的锁定状态不一致。

```
#include <iostream>
#include <thread>
#include <mutex>
#include <vector> 
using namespace std;
// 共享变量，使用互斥锁保护
int counter = 0;
mutex counter_mutex; // 互斥锁

// 线程函数，对counter进行递增操作
void sum() {
	lock_guard<mutex> guard(counter_mutex);
    for (int i = 0; i < 10000; ++i) {
        
        
        
        counter++;
      
    }
}

int main() {
	vector<thread> mybox;
    
   	for(int i=0;i<10;i++)
   	mybox.emplace_back(sum);
    
    for(thread& t:mybox)
    t.join();
    cout<<"cout is "<<counter<<endl; 
    return 0;
}
```

### 7.线程间同步方式

1.线程同步的方式
一般来说 ，我们要完成线程之间的同步和协作**(也就是控制线程与线程之间的执行顺序或者资源争用)**，往往会有下面几种方式。顺序资源争用，线程之间不够同步

<img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240718160528846.png" alt="image-20240718160528846" style="zoom:80%;" />

### 8.条件变量 condition_variable

**1.啥是条件变量**
条件变量是一种同步原语，用于在线程之间协调共享资源的访问。它允许一个线程等待特定条件的满足(如某个值的变化)，而另一个线程在条件满足时通知(或唤醒)等待的线程。这种机制可以防止线程忙等待，从而提高系统效率。

<img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240718161044647.png" alt="image-20240718161044647" style="zoom:80%;" />

条件变量是一种同步原语，用于在线程之间协调共享资源的访问。它允许一个线程等待特定条件的满足(如某个值的变化)，而另一个线程在条件满足时通知(或唤醒)等待的线程。这种机制可以防止线程忙等待，从而提高系统效率。

```
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>

std::mutex mtx; // 互斥锁
std::condition_variable cv; // 条件变量
std::queue<int> queue; // 队列

void producer() {
    for (int i = 0; i < 10; ++i) {
        std::unique_lock<std::mutex> lock(mtx);
        queue.push(i); // 将数据放入队列
        std::cout << "Produced: " << i << std::endl;
        lock.unlock();
        cv.notify_one(); // 通知消费者队列中有数据
    }
}

void consumer() {
    while (true) {
        std::unique_lock<std::mutex> lock(mtx);
        cv.wait(lock, []{ return !queue.empty(); }); // 等待队列不为空
        int data = queue.front(); // 获取队列前端的元素
        queue.pop(); // 从队列中移除元素
        std::cout << "Consumed: " << data << std::endl;
        if (data == 9) break; // 如果是最后一个元素，则退出循环
        lock.unlock();
    }
}

int main() {
    std::thread t1(producer);
    std::thread t2(consumer);

    t1.join();
    t2.join();

    return 0;
}

```

在C++中，`std::condition_variable` 类提供了几个常用的函数，用于线程之间的同步。以下是这些函数的简要说明：

1. `std::condition_variable::notify_one()`
   - 唤醒等待队列中的一个线程。如果有多个线程在等待，`notify_one()` 会随机选择一个线程唤醒。
   - 参数：无。
   - 返回值：无。
2. `std::condition_variable::notify_all()`
   - 唤醒等待队列中的所有线程。
   - 参数：无。
   - 返回值：无。
3. `std::condition_variable::wait()`
   - 等待条件变量，直到另一个线程调用 `notify_one()` 或 `notify_all()`。
   - 参数：
     - 一个 `std::unique_lock` 对象，用于保护互斥锁。
     - 一个条件谓词（condition predicate），用于判断条件是否满足。如果条件满足，`wait()` 返回，并继续执行；如果条件不满足，线程会阻塞，直到条件满足或被另一个线程通过 `notify_one()` 或 `notify_all()` 唤醒。
   - 返回值：无。
4. `std::condition_variable::wait_for()`
   - 等待条件变量，直到另一个线程调用 `notify_one()` 或 `notify_all()`，或者等待指定的时间量（duration）。
   - 参数：
     - 一个 `std::unique_lock` 对象，用于保护互斥锁。
     - 一个时间量（duration），用于指定等待时间。
     - 一个条件谓词（condition predicate），用于判断条件是否满足。
   - 返回值：
     - 如果条件在指定时间内满足，返回 `std::cv_status::no_timeout`。
     - 如果条件在指定时间内不满足，返回 `std::cv_status::timeout`。
5. `std::condition_variable::wait_until()`
   - 等待条件变量，直到另一个线程调用 `notify_one()` 或 `notify_all()`，或者等待指定的时间点（time point）。
   - 参数：
     - 一个 `std::unique_lock` 对象，用于保护互斥锁。
     - 一个时间点（time point），用于指定等待时间。
     - 一个条件谓词（condition predicate），用于判断条件是否满足。
   - 返回值：
     - 如果条件在指定时间点之前满足，返回 `std::cv_status::no_timeout`。
     - 如果条件在指定时间点之后仍然不满足，返回 `std::cv_status::timeout`。

这些函数通常与 `std::mutex` 一起使用，以确保在调用 `wait()` 函数时不会丢失对共享资源的锁。