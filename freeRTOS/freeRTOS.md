# FreeRTOS学习笔记

## 第一章  任务管理

### 1.1 创建任务

**xTaskCreate() API函数**

创建任务使用 FreeRTOS 的 API 函数 xTaskCreate()。这可能是所有 API 函数中最复杂的函数，但不幸的是这也是我们第一个遇到的 API 函数。但我们必须首先掌控任务，因为它们是多任务系统中最基本的组件。

**xTaskCreate() 函数原型**

```c
portBASE_TYPE xTaskCreate(pdTASK_CODE pvTaskCode,
                          const signed portCHAR * const pcName,
                          unsigned portSHORT usStackDepth,
                          void *pvParameters,
                          unsigned portBASE_TYPE uxPriority,
                          xTaskHandle *pxCreatedTask );
```

**表 xTaskCreate()参数与返回值**

| 参数名        | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| pvTaskCode    | 任务只是永不退出的 C 函数，实现常通常是一个死循环。参数pvTaskCode 只一个指向任务的实现函数的指针(效果上仅仅是函数名)。 |
| pcName        | 具有描述性的任务名。这个参数不会被 FreeRTOS 使用。其只是单纯地用于辅助调试。识别一个具有可读性的名字总是比通过句柄来识别容易得多。<br />应用程序可以通过定义常量 config_MAX_TASK_NAME_LEN 来定义任务名的最大长度——包括’\0’结束符。如果传入的字符串长度超过了这个最大值，字符串将会自动被截断。 |
| usStackDepth  | 当任务创建时，内核会分为每个任务分配属于任务自己的唯一状态。usStackDepth 值用于告诉内核为它分配多大的栈空间。<br />这个值指定的是栈空间可以保存多少个字(word)，而不是多少个字节(byte)。比如说，如果是 32 位宽的栈空间，传入的 usStackDepth值为 100，则将会分配 400 字节的栈空间(100 * 4bytes)。栈深度乘以栈宽度的结果千万不能超过一个 size_t 类型变量所能表达的最大值。<br />应用程序通过定义常量 configMINIMAL_STACK_SIZE 来决定空闲任务任用的栈空间大小。在 FreeRTOS 为微控制器架构提供的Demo 应用程序中，赋予此常量的值是对所有任务的最小建议值。如果你的任务会使用大量栈空间，那么你应当赋予一个更大的值。<br />没有任何简单的方法可以决定一个任务到底需要多大的栈空间。计算出来虽然是可能的，但大多数用户会先简单地赋予一个自认为合理的值，然后利用 FreeRTOS 提供的特性来确证分配的空间既不欠缺也不浪费。第六章包括了一些信息，可以知道如何去查询任务使用了多少栈空间。 |
| pvParameters  | 任务函数接受一个指向 void 的指针(void*)。pvParameters 的值即是传递到任务中的值。这篇文档中的一些范例程序将会示范这个参数可以如何使用。 |
| uxPriority    | 指定任务执行的优先级。优先级的取值范围可以从最低优先级 0 到最高优先级(configMAX_PRIORITIES – 1)。 |
| pxCreatedTask | pxCreatedTask 用于传出任务的句柄。这个句柄将在 API 调用中对该创建出来的任务进行引用，比如改变任务优先级，或者删除任务。如果应用程序中不会用到这个任务的句柄，则 pxCreatedTask 可以被设为 NULL。 |
| 返回值        | 有两个可能的返回值：<br/>1. pdTRUE<br/>表明任务创建成功。<br/>2. errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY<br/>由于内存堆空间不足，FreeRTOS 无法分配足够的空间来保存任务<br/>结构数据和任务栈，因此无法创建任务。 |

### 1.2 扩充非运行状态

#### 阻塞状态

如果一个任务正在等待某个事件，则称这个任务处于”阻塞态(blocked)”。阻塞态是
非运行态的一个子状态。
任务可以进入阻塞态以等待以下两种不同类型的事件：
1. 定时(时间相关)事件——这类事件可以是延迟到期或是绝对时间到点。比如
    说某个任务可以进入阻塞态以延迟 10ms。
2. 同步事件——源于其它任务或中断的事件。比如说，某个任务可以进入阻塞
    态以等待队列中有数据到来。同步事件囊括了所有板级范围内的事件类型。

利用阻塞态实现延时

**vTaskDelay()API 函数**

```c
void vTaskDelay( portTickType xTicksToDelay );
```

**表 xTaskDelay()参数**

|    参数名     | 描述                                                         |
| :-----------: | :----------------------------------------------------------- |
| xTicksToDelay | 延迟多少个心跳周期。调用该延迟函数的任务将进入阻塞态，经延迟指定的心跳周期数后，再转移到就绪态。举个例子，当某个任务调用 vTaskDelay( 100 )时，心跳计数值为 10,000，则该任务将保持在阻塞态，直到心跳计数计到10,100。常数 portTICK_RATE_MS 可以用来将以毫秒为单位的时间值转换为以心跳周期为单位的时间值。 |

例子

```c
void vTaskFunction( void *pvParameters )
{
	char *pcTaskName;
/* The string to print out is passed in via the parameter. Cast this to a
character pointer. */
	pcTaskName = ( char * ) pvParameters;
/* As per most tasks, this task is implemented in an infinite loop. */
	for( ;; )
	{
		/* Print out the name of this task. */
		vPrintString( pcTaskName );
/* 延迟一个循环周期。调用vTaskDelay()以让任务在延迟期间保持在阻塞态。延迟时间以心跳周期为
单位，常量portTICK_RATE_MS可以用来在毫秒和心跳周期之间相换转换。本例设定250毫秒的循环周
期。 */
		vTaskDelay( 250 / portTICK_RATE_MS );
	}
}
```



**vTaskDelayUntil() API 函数**

vTaskDelayUntil()类似于 vTaskDelay()。和范例中演示的一样，函数 vTaskDelay()
的参数用来指定任务在调用 vTaskDelay()到切出阻塞态整个过程包含多少个心跳周期。
任务保持在阻塞态的时间量由 vTaskDelay()的入口参数指定，但任务离开阻塞态的时刻
实际上是相对于 vTaskDelay()被调用那一刻的。vTaskDelayUntil()的参数就是用来指定
任务离开阻塞态进入就绪态那一刻的精确心跳计数值。 API 函数 vTaskDelayUntil()可以
用于实现一个固定执行周期的需求(当你需要让你的任务以固定频率周期性执行的时
候)。由于调用此函数的任务解除阻塞的时间是绝对时刻，比起相对于调用时刻的相对
时间更精确(即比调用 vTaskDelay()可以实现更精确的周期性)。

```c
void vTaskDelayUntil( portTickType * pxPreviousWakeTime, portTickType xTimeIncrement );
```

**表  vTaskDelayUntil()参数**

| 参数名             | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| pxPreviousWakeTime | 此参数命名时假定 vTaskDelayUntil()用于实现某个任务以固定频率周期性执行。这种情况下 pxPreviousWakeTime保存了任务上一次离开阻塞态(被唤醒)的时刻。这个时刻被用作一个参考点来计算该任务下一次离开阻塞态的时刻。<br/>pxPreviousWakeTime 指 向 的 变 量 值 会 在 API 函 数vTaskDelayUntil()调用过程中自动更新，应用程序除了该变量第一次初始化外，通常都不要修改它的值。 |
| xTimeIncrement     | 此参数命名时同样是假定 vTaskDelayUntil()用于实现某个任 务 以 固 定 频 率 周 期 性 执 行 —— 这 个 频 率 就 是 由xTimeIncrement 指定的。<br/>xTimeIncrement 的 单 位 是 心 跳 周 期 ， 可 以 使 用 常 量portTICK_RATE_MS 将毫秒转换为心跳周期。 |

例子

```c
void vTaskFunction( void *pvParameters )
{
	char *pcTaskName;
	portTickType xLastWakeTime;
/* The string to print out is passed in via the parameter. Cast this to a
character pointer. */
	pcTaskName = ( char * ) pvParameters;
/* 变量xLastWakeTime需要被初始化为当前心跳计数值。说明一下，这是该变量唯一一次被显式赋值。之后，
xLastWakeTime将在函数vTaskDelayUntil()中自动更新。 */
	xLastWakeTime = xTaskGetTickCount();
/* As per most tasks, this task is implemented in an infinite loop. */
	for( ;; )
	{
/* Print out the name of this task. */
		vPrintString( pcTaskName );
/* 本任务将精确的以250毫秒为周期执行。同vTaskDelay()函数一样，时间值是以心跳周期为单位的，
可以使用常量portTICK_RATE_MS将毫秒转换为心跳周期。变量xLastWakeTime会在
vTaskDelayUntil()中自动更新，因此不需要应用程序进行显示更新。 */
		vTaskDelayUntil( &xLastWakeTime, ( 250 / portTICK_RATE_MS ) );
	}
}
```



#### 挂起状态

“挂起(suspended)”也是非运行状态的子状态。处于挂起状态的任务对调度器而言
是不可见的。

让一个任务进入挂起状态的唯一办法就是调用 vTaskSuspend() API 函数；
而 把 一 个 挂 起 状 态 的 任 务 唤 醒 的 唯 一 途 径 就 是 调 用 vTaskResume() 或
vTaskResumeFromISR() API 函数。

#### 就绪状态

如果任务处于非运行状态，但既没有阻塞也没有挂起，则这个任务处于就绪(ready，
准备或就绪)状态。处于就绪态的任务能够被运行，但只是”准备(ready)”运行，而当前
尚未运行。

<img src="/home/sunny/Nutstore Files/我的坚果云/TyporaFile/freeRTOS/完整任务机状态.png" style="zoom:50%;" />

### 1.3 空闲任务与空闲任务钩子函数

处理器总是需要代码来执行——所以至少要有一个任务处于运行态。为了保证这
一点，当调用 vTaskStartScheduler()时，调度器会自动创建一个空闲任务。空闲任务是
一个非常短小的循环——和最早的示例任务十分相似，总是可以运行。
空闲任务拥有最低优先级(优先级 0)以保证其不会妨碍具有更高优先级的应用任务
进入运行态——当然，没有任何限制说是不能把应用任务创建在与空闲任务相同的优先
级上；如果需要的话，你一样可以和空闲任务一起共享优先级。

#### 空闲任务钩子函数

通过空闲任务钩子函数(或称回调，hook, or call-back)，可以直接在空闲任务中添
加应用程序相关的功能。空闲任务钩子函数会被空闲任务每循环一次就自动调用一次。
通常空闲任务钩子函数被用于：

- 执行低优先级，后台或需要不停处理的功能代码。
- 测试处系统处理裕量(空闲任务只会在所有其它任务都不运行时才有机会执行，所
  以测量出空闲任务占用的处理时间就可以清楚的知道系统有多少富余的处理时间)。
- 将处理器配置到低功耗模式——提供一种自动省电方法，使得在没有任何应用功能
  需要处理的时候，系统自动进入省电模式。

#### 空闲任务钩子函数的实现限制

空闲任务钩子函数必须遵从以下规则

- 绝不能阻塞或挂起。空闲任务只会在其它任务都不运行时才会被执行(除非有应用任
  务共享空闲任务优先级)。以任何方式阻塞空闲任务都可能导致没有任务能够进入
  运行态！

- 如果应用程序用到了 vTaskDelete() AP 函数，则空闲钩子函数必须能够尽快返回。
  因为在任务被删除后，空闲任务负责回收内核资源。如果空闲任务一直运行在钩
  子函数中，则无法进行回收工作。

**空闲任务钩子函数**

```c
void vApplicationIdleHook( void )
```

例子

```c
/* Declare a variable that will be incremented by the hook function. */
unsigned long ulIdleCycleCount = 0UL;
/* 空闲钩子函数必须命名为vApplicationIdleHook(),无参数也无返回值。 */
void vApplicationIdleHook( void )
{
/* This hook function does nothing but increment a counter. */
	ulIdleCycleCount++;
}
```

FreeRTOSConfig.h 中的配置常量 configUSE_IDLE_HOOK 必须定义为 1，这样空
闲任务钩子函数才会被调用。

### 1.4 改变任务优先级

**vTaskPrioritySet() API 函数(改变任务优先级)**

```c
void vTaskPrioritySet( xTaskHandle pxTask, unsigned portBASE_TYPE uxNewPriority );
```

**表 vTaskPrioritySet() 参数**

| 参数名        | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| pxTask        | 被修改优先级的任务句柄(即目标任务)——参考 xTaskCreate() API函数的参数 pxCreatedTask 以了解如何得到任务句柄方面的信息。任务可以通过传入 NULL 值来修改自己的优先级。 |
| uxNewPriority | 目标任务将被设置到哪个优先级上。如果设置的值超过了最大可用优先级(configMAX_PRIORITIES – 1)，则会被自动封顶为最大值。常量 configMAX_PRIORITIES 是在 FreeRTOSConfig.h 头文件中设置的一个编译时选项。 |

**uxTaskPriorityGet() API 函数(查询任务优先级)**

```c
unsigned portBASE_TYPE uxTaskPriorityGet( xTaskHandle pxTask );
```

**表  uxTaskPriorityGet()参数及返回值**

| 参数名 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| pxTask | 被查询任务的句柄(目标任务) ——参考 xTaskCreate() API 函数的参数pxCreatedTask 以了解如何得到任务句柄方面的信息。任务可以通过传入 NULL 值来查询自己的优先级。 |
| 返回值 | 被查询任务的当前优先级。                                     |

例子

```c
void vTask1( void *pvParameters )
{
	unsigned portBASE_TYPE uxPriority;
/* 本任务将会比任务2更先运行，因为本任务创建在更高的优先级上。任务1和任务2都不会阻塞，所以两者要么处于就绪态，要么处于运行态。
查询本任务当前运行的优先级 – 传递一个NULL值表示说“返回我自己的优先级”。 */
	uxPriority = uxTaskPriorityGet( NULL );
	for( ;; )
	{
		/* Print out the name of this task. */
		vPrintString( "Task1 is running\r\n" );
/* 把任务2的优先级设置到高于任务1的优先级，会使得任务2立即得到执行(因为任务2现在是所有任务
中具有最高优先级的任务)。注意调用vTaskPrioritySet()时用到的任务2的句柄。程序清单24将展示
如何得到这个句柄。 */
		vPrintString( "About to raise the Task2 priority\r\n" );
		vTaskPrioritySet( xTask2Handle, ( uxPriority + 1 ) );
/* 本任务只会在其优先级高于任务2时才会得到执行。因此，当此任务运行到这里时，任务2必然已经执
行过了，并且将其自身的优先级设置回比任务1更低的优先级。 */
	}
}
```

```c
/* 声明变量用于保存任务2的句柄。 */
xTaskHandle xTask2Handle;
int main( void )
{
/* 任务1创建在优先级2上。任务参数没有用到，设为NULL。任务句柄也不会用到，也设为NULL */
	xTaskCreate( vTask1, "Task 1", 1000, NULL, 2, NULL );
/* The task is created at priority 2 ______^. */
/* 任务2创建在优先级1上 – 此优先级低于任务1。任务参数没有用到，设为NULL。但任务2的任务句柄会被
用到，故将xTask2Handle的地址传入。 */
	xTaskCreate( vTask2, "Task 2", 1000, NULL, 1, &xTask2Handle );
/* The task handle is the last parameter _____^^^^^^^^^^^^^ */
/* Start the scheduler so the tasks start executing. */
	vTaskStartScheduler();
/* If all is well then main() will never reach here as the scheduler will
now be running the tasks. If main() does reach here then it is likely that
there was insufficient heap memory available for the idle task to be created.
CHAPTER 5 provides more information on memory management. */
	for( ;; );
}
```

### 1.5 删除任务

**vTaskDelete() API 函数**

任务可以使用 API 函数 vTaskDelete()删除自己或其它任务。
任务被删除后就不复存在，也不会再进入运行态。
空闲任务的责任是要将分配给已删除任务的内存释放掉。因此有一点很重要，那就
是使用 vTaskDelete() API 函数的任务千万不能把空闲任务的执行时间饿死。
需要说明一点，只有内核为任务分配的内存空间才会在任务被删除后自动回收。任
务自己占用的内存或资源需要由应用程序自己显式地释放。

```c
void vTaskDelete( xTaskHandle pxTaskToDelete );
```

**表  vTaskDelete()参数**

| 参数名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| pxTaskToDelete | 被删除任务的句柄(目标任务) —— 参考 xTaskCreate() API 函数的参数 pxCreatedTask 以了解如何得到任务句柄方面的信息。任务可以通过传入 NULL 值来删除自己。 |

例子

```c
void vTask2( void *pvParameters )
{
/* 任务2什么也没做，只是删除自己。删除自己可以传入NULL值，这里为了演示，还是传入其自己的句柄。 */
	vPrintString( "Task2 is running and about to delete itself\r\n" );
	vTaskDelete( xTask2Handle );
}
```

### 1.6 调度算法简述

**优先级抢占式调度**

- 每个任务都赋予了一个优先级。
- 每个任务都可以存在于一个或多个状态。
- 在任何时候都只有一个任务可以处于运行状态。
- 调度器总是在所有处于就绪态的任务中选择具有最高优先级的任务来执行。
  这种类型的调度方案被称为”固定优先级抢占式调度”。所谓”固定优先级”是指每个任务都被赋予了一个优先级，这个优先级不能被内核本身改变(只能被任务修改)。”抢占式”是指当任务进入就绪态或是优先级被改变时，如果处于运行态的任务优先级更低，则该任务总是抢占当前运行的任务。
  任务可以在阻塞状态等待一个事件，当事件发生时其将自动回到就绪态。时间事件发生在某个特定的时刻，比如阻塞超时。时间事件通常用于周期性或超时行为。任务或中断服务例程往队列发送消息或发送任务一种信号量，都将触发同步事件。同步事件通常用于触发同步行为，比如某个外围的数据到达了。

**选择任务优先级**

作为一种通用规则，完成硬实时功能的任务优先级会高于完成软件时功能任务的优先级。但其它一些因素，比如执行时间和处理器利用率，都必须纳入考虑范围，以保证应用程序不会超过硬实时的需求限制。单调速率调度(Rate Monotonic Scheduling, RMS)是一种常用的优先级分配技术。其根据任务周期性执行的速率来分配一个唯一的优先级。具有最高周期执行频率的任务赋予高最优先级；具有最低周期执行频率的任务赋予最低优先级。这种优先级分配方式被证明了可以最大化整个应用程序的可调度性(schedulability)，但是运行时间不定以及并非所有任务都具有周期性，会使得对这种方式的全面计算变得相当复杂。

**协作式调度**

采用一个纯粹的协作式调度器，只可能在运行态任务进入阻塞态或是运行态任务显式调用 taskYIELD()时，才会进行上下文切换。任务永远不会被抢占，而具有相同优先级的任务也不会自动共享处理器时间。协作式调度的这作工作方式虽然比较简单，但可能会导致系统响应不够快。实现混合调度方案也是可行的，这需要在中断服务例程中显式地进行上下文切换，从而允许同步事件产生抢占行为，但时间事件却不行。这样做的结果是得到了一个没有时间片机制的抢占式系统。或许这正是所期望的，因为获得了效率，并且这也是一种常用的调度器配置。

## 第二章 队列管理

基于 FreeRTOS 的应用程序由一组独立的任务构成——每个任务都是具有独立权限的小程序。这些独立的任务之间很可能会通过相互通信以提供有用的系统功能。FreeRTOS 中所有的通信与同步机制都是基于队列实现的。

### 2.1 队列的特性

**数据存储**

队列可以保存的最大单元数目被称为队列的“深度”。在队列创建时需要设定其深度和每个单元的大小。

**可被多任务存取**

队列是具有自己独立权限的内核对象，并不属于或赋予任何任务。所有任务都可以向同一队列写入和读出。

**读队列时阻塞**

当某个任务试图读一个队列时，其可以指定一个阻塞超时时间。在这段时间中，如果队列为空，该任务将保持阻塞状态以等待队列数据有效。当其它任务或中断服务例程往其等待的队列中写入了数据，该任务将自动由阻塞态转移为就绪态。当等待的时间超过了指定的阻塞时间，即使队列中尚无有效数据，任务也会自动从阻塞态转移为就绪态。由于队列可以被多个任务读取，所以对单个队列而言，也可能有多个任务处于阻塞状态以等待队列数据有效。这种情况下，一旦队列数据有效，只会有一个任务会被解除阻塞，这个任务就是所有等待任务中优先级最高的任务。而如果所有等待任务的优先级
相同，那么被解除阻塞的任务将是等待最久的任务。

**写队列时阻塞**

同读队列一样，任务也可以在写队列时指定一个阻塞超时时间。这个时间是当被写队列已满时，任务进入阻塞态以等待队列空间有效的最长时间。由于队列可以被多个任务写入，所以对单个队列而言，也可能有多个任务处于阻塞状态以等待队列空间有效。这种情况下，一旦队列空间有效，只会有一个任务会被解除阻塞，这个任务就是所有等待任务中优先级最高的任务。而如果所有等待任务的优先级
相同，那么被解除阻塞的任务将是等待最久的任务。

### 2.2 使用队列

- 队列在使用前必须先被创建。
- 队列由声明为 xQueueHandle 的变量进行引用。xQueueCreate()用于创建一个队
  列，并返回一个 xQueueHandle 句柄以便于对其创建的队列进行引用。
- 当创建队列时，FreeRTOS 从堆空间中分配内存空间。分配的空间用于存储队列数
  据结构本身以及队列中包含的数据单元。如果内存堆中没有足够的空间来创建队列，
  xQueueCreate()将返回 NULL。

**xQueueCreate() API 函数**

```c
xQueueHandle xQueueCreate( unsigned portBASE_TYPE uxQueueLength,
unsigned portBASE_TYPE uxItemSize );
```

**表  xQueueCreate()参数与返回值**

| 参数名        | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| uxQueueLength | 队列能够存储的最大单元数目，即队列深度。                     |
| uxItemSize    | 队列中数据单元的长度，以字节为单位。                         |
| 返回值        | NULL 表示没有足够的堆空间分配给队列而导致创建失败。<br/>非 NULL 值表示队列创建成功。此返回值应当保存下来，以作为<br/>操作此队列的句柄。 |

**xQueueSendToBack() 与 xQueueSendToFront() API 函数**

xQueueSendToBack()用于将数据发送到队列尾；而 xQueueSendToFront()用于将数据发送到队首。

xQueueSend()完全等同于 xQueueSendToBack()。

**注意：**不 要 在 中 断 服 务 例 程 中 调 用xQueueSendToFront() 或xQueueSendToBack()。系统提供中断安全版本的 xQueueSendToFrontFromISR()与xQueueSendToBackFromISR()用于在中断服务中实现相同的功能。

```c
portBASE_TYPE xQueueSendToFront(xQueueHandle xQueue,const void * pvItemToQueue,
                               portTickType xTicksToWait );

portBASE_TYPE xQueueSendToBack(xQueueHandle xQueue,const void * pvItemToQueue,
                               portTickType xTicksToWait );
```

**表  xQueueSendToFront()与 xQueueSendToBack()函数参数及返回值**

| 参数名        | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| xQueue        | 目标队列的句柄。这个句柄即是调用 xQueueCreate()创建该队列时的返回值。 |
| pvItemToQueue | 发送数据的指针。其指向将要复制到目标队列中的数据单元。由于在创建队列时设置了队列中数据单元的长度，所以会从该指针指向的空间复制对应长度的数据到队列的存储区域。 |
| xTicksToWait  | 阻塞超时时间。如果在发送时队列已满，这个时间即是任务处于阻塞态等待队列空间有效的最长等待时间。如 果 xTicksToWait 设 为 0 ， 并 且 队 列 已 满 ， 则xQueueSendToFront()与 xQueueSendToBack()均会立即返回。阻塞时间是以系统心跳周期为单位的，所以绝对时间取决于系统心跳频率。常量 portTICK_RATE_MS 可以用来把心跳时间单位转换为毫秒时间单位。如 果 把 xTicksToWait 设 置 为 portMAX_DELAY ， 并 且 在FreeRTOSConig.h 中设定 INCLUDE_vTaskSuspend 为 1，那么阻塞等待将没有超时限制。 |
| 返回值        | 有两个可能的返回值:<br>1. pdPASS<br/>返回 pdPASS 只会有一种情况，那就是数据被成功发送到队列中。如果设定了阻塞超时时间(xTicksToWait 非 0)，在函数返回之前任务将被转移到阻塞态以等待队列空间有效—在超时到来前能够将数据成功写入到队列，函数则会返回 pdPASS。<br/>2. errQUEUE_FULL<br/>如果由于队列已满而无法将数据写入，则将返回errQUEUE_FULL。如果设定了阻塞超时时间（xTicksToWait 非 0），在函数返回之前任务将被转移到阻塞态以等待队列空间有效。但直到超时也没有其它任务或是中断服务例程读取队列而腾出空间，函数则会返回 errQUEUE_FULL。 |

**xQueueReceive()与 xQueuePeek() API 函数**

xQueueReceive()用于从队列中接收(读取）数据单元。接收到的单元同时会从队列中删除。
xQueuePeek()也是从从队列中接收数据单元，不同的是并不从队列中删出接收到的单元xQueuePeek()从队列首接收到数据后，不会修改队列中的数据，也不会改变数据在队列中的存储序顺。

**注意：**不要在中断服务例程中调用 xQueueRceive()和 xQueuePeek()。中断安全版本的替代 API 函数为 xQueueReceiveFromISR()。

```c
portBASE_TYPE xQueueReceive(xQueueHandle xQueue,const void * pvBuffer,
                             portTickType xTicksToWait );
portBASE_TYPE xQueuePeek(xQueueHandle xQueue,const void * pvBuffer,
                              portTickType xTicksToWait );
```

**表  xQueueReceive()与 xQueuePeek()函数参数与返回值**

| 参数名       | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| xQueue       | 被读队列的句柄。这个句柄即是调用 xQueueCreate()创建该队列时的返回值。 |
| pvBuffer     | 接收缓存指针。其指向一段内存区域，用于接收从队列中拷贝来的数据。 |
| xTicksToWait | 阻塞超时时间。如果在接收时队列为空，则这个时间是任务处于阻塞状态以等待队列数据有效的最长等待时间。如果 xTicksToWait 设为 0，并且队列为空，则 xQueueRecieve()与 xQueuePeek()均会立即返回。阻塞时间是以系统心跳周期为单位的，所以绝对时间取决于系统心跳频率。常量 portTICK_RATE_MS 可以用来把心跳时间单位转换为毫秒时间单位。如 果 把 xTicksToWait 设 置 为 portMAX_DELAY ， 并 且 在FreeRTOSConig.h 中设定 INCLUDE_vTaskSuspend 为 1，那么阻塞等待将没有超时限制。 |
| 返回值       | 有两个可能的返回值:<br/>1. pdPASS<br/>返回 pdPASS 只会有一种情况，那就是数据被成功发送到队列中。如果设定了阻塞超时时间(xTicksToWait 非 0)，在函数返回之前任务将被转移到阻塞态以等待队列空间有效—在超时到来前能够将数据成功写入到队列，函数则会返回 pdPASS。<br/>2. errQUEUE_FULL<br/>如果由于队列已满而无法将数据写入，则将返回errQUEUE_FULL。如果设定了阻塞超时时间（xTicksToWait 非 0），在函数返回之前任务将被转移到阻塞态以等待队列空间有效。但直到超时也没有其它任务或是中断服务例程读取队列而腾出空间，函数则会返回 errQUEUE_FULL。 |

**uxQueueMessagesWaiting() API 函数**

uxQueueMessagesWaiting()用于查询队列中当前有效数据单元个数。

**注意:**不要在中断服务例程中调用 uxQueueMessagesWaiting()。应当在中断服务中使用其中断安全版本 uxQueueMessagesWaitingFromISR()。

```c
unsigned portBASE_TYPE uxQueueMessagesWaiting( xQueueHandle xQueue );
```

**表  uxQueueMessagesWaiting()函数参数及返回值**

| 参数名 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| xQueue | 被查询队列的句柄。这个句柄即是调用 xQueueCreate()创建该队列时的返回值。 |
| 返回值 | 当前队列中保存的数据单元个数。返回 0 表明队列为空。          |

**例 读队列时阻塞**

```c
//写队列实现代码

static void vSenderTask( void *pvParameters )
{
	long lValueToSend;
	portBASE_TYPE xStatus;
/* 该任务会被创建两个实例，所以写入队列的值通过任务入口参数传递 – 这种方式使得每个实例使用不同的
值。队列创建时指定其数据单元为long型，所以把入口参数强制转换为数据单元要求的类型 */
	lValueToSend = ( long ) pvParameters;
/* 和大多数任务一样，本任务也处于一个死循环中 */
	for( ;; )
	{
/* 往队列发送数据
第一个参数是要写入的队列。队列在调度器启动之前就被创建了，所以先于此任务执行。
第二个参数是被发送数据的地址，本例中即变量lValueToSend的地址。
第三个参数是阻塞超时时间 – 当队列满时，任务转入阻塞状态以等待队列空间有效。本例中没有设定超
时时间，因为此队列决不会保持有超过一个数据单元的机会，所以也决不会满。
*/
		xStatus = xQueueSendToBack( xQueue, &lValueToSend, 0 );
		if( xStatus != pdPASS )
		{
/* 发送操作由于队列满而无法完成 – 这必然存在错误，因为本例中的队列不可能满。 */
			vPrintString( "Could not send to the queue.\r\n" );
		}
/* 允许其它发送任务执行。 taskYIELD()通知调度器现在就切换到其它任务，而不必等到本任务的时
间片耗尽 */
		taskYIELD();
	}
}

//读队列任务实现代码

static void vReceiverTask( void *pvParameters )
{
/* 声明变量，用于保存从队列中接收到的数据。 */
	long lReceivedValue;
	portBASE_TYPE xStatus;
	const portTickType xTicksToWait = 100 / portTICK_RATE_MS;
/* 本任务依然处于死循环中。 */
	for( ;; )
	{
/* 此调用会发现队列一直为空，因为本任务将立即删除刚写入队列的数据单元。 */
		if( uxQueueMessagesWaiting( xQueue ) != 0 )
		{
			vPrintString( "Queue should have been empty!\r\n" );
		}
/* 从队列中接收数据
第一个参数是被读取的队列。队列在调度器启动之前就被创建了，所以先于此任务执行。
第二个参数是保存接收到的数据的缓冲区地址，本例中即变量lReceivedValue的地址。此变量类型与
队列数据单元类型相同，所以有足够的大小来存储接收到的数据。
第三个参数是阻塞超时时间 – 当队列空时，任务转入阻塞状态以等待队列数据有效。本例中常量
portTICK_RATE_MS用来将100毫秒绝对时间转换为以系统心跳为单位的时间值。
*/
		xStatus = xQueueReceive( xQueue, &lReceivedValue, xTicksToWait );
		if( xStatus == pdPASS )
		{
/* 成功读出数据，打印出来。 */
			vPrintStringAndNumber( "Received = ", lReceivedValue );
		}
		else
		{
/* 等待100ms也没有收到任何数据。
必然存在错误，因为发送任务在不停地往队列中写入数据 */
			vPrintString( "Could not receive from the queue.\r\n" );
		}
	}
}

//main函数实现代码

/* 声明一个类型为 xQueueHandle 的变量. 其用于保存队列句柄，以便三个任务都可以引用此队列 */
xQueueHandle xQueue;
int main( void )
{
/* 创建的队列用于保存最多5个值，每个数据单元都有足够的空间来存储一个long型变量 */
	xQueue = xQueueCreate( 5, sizeof( long ) );
	if( xQueue != NULL )
	{
/* 创建两个写队列任务实例，任务入口参数用于传递发送到队列的值。所以一个实例不停地往队列发送
100，而另一个任务实例不停地往队列发送200。两个任务的优先级都设为1。 */
		xTaskCreate( vSenderTask, "Sender1", 1000, ( void * ) 100, 1, NULL );
		xTaskCreate( vSenderTask, "Sender2", 1000, ( void * ) 200, 1, NULL );
/* 创建一个读队列任务实例。其优先级设为2，高于写任务优先级 */
		xTaskCreate( vReceiverTask, "Receiver", 1000, NULL, 2, NULL );
/* 启动调度器，任务开始执行 */
		vTaskStartScheduler();
	}
	else
	{
/* 队列创建失败*/
	}
/* 如果一切正常，main()函数不应该会执行到这里。但如果执行到这里，很可能是内存堆空间不足导致空闲
任务无法创建。第五章有讲述更多关于内存管理方面的信息 */
	for( ;; );
}
```

## 第三章 中断管理

嵌入式实时系统需要对整个系统环境产生的事件作出反应。举个例子，以太网外围部件收到了一个数据包(事件)，需要送到 TCP/IP 协议栈进行处理(反应)。更复杂的系统需要处理来自各种源头产生的事件，这些事件对处理时间和响应时间都有不同的要求。
在各种情况下，都需要作出合理的判断，以达到最佳事件处理的实现策略：

1. 事件如何被检测到？通常采用中断方式，但是事件输入也可以通过查询获得。
2. 什么时候采用中断方式？中断服务例程(ISR)中的处理量有多大？以及 ISR 外的任
务量有多大？通常情况下，ISR 应当越短越好。
3. 事件如何通知到主程序(这里指非 ISR 程序，而非 main()程序)代码？这些代码要如
何架构才能最好地适应异步处理？
FreeRTOS 并没有为设计人员提供具体的事件处理策略，但是提供了一些特性使得
设计人员采用的策略可以得到实现，而实现方式不仅简单，而且具有可维护性。
**必须说明的是，只有以”FromISR”或”FROM_ISR”结束的 API 函数或宏才可以在中
断服务例程中。**

目标：

- 哪些 FreeRTOS 的 API 函数可以在中断服务例程中使用。
- 延迟中断方案是处何实现的。
- 如何创建和使用二值信号量以及计数信号量。
- 二值信号量和计数信号量之间的区别。
- 如何利用队利在中断服务例程中把数据传入传出。
- 一些 FreeRTOS 移植中采用的中断嵌套模型。

### 3.1 延迟中断处理

**采用二值信号量同步**

二值信号量可以在某个特殊的中断发生时，让任务解除阻塞，相当于让任务与中断同步。这样就可以让中断事件处理量大的工作在同步任务中完成，中断服务例程(ISR)中只是快速处理少部份工作。如此，中断处理可以说是被”推迟(deferred)”到一个”处理(handler)”任务。

如果某个中断处理要求特别紧急，其延迟处理任务的优先级可以设为最高，以保证延迟处理任务随时都抢占系统中的其它任务。这样，延迟处理任务就成为其对应的 ISR退出后第一个执行的任务，在时间上紧接着 ISR 执行，相当于所有的处理都在 ISR 中完成一样。

延迟处理任务对一个信号量进行带阻塞性质的”take”调用，意思是进入阻塞态以等待事件发生。当事件发生后，ISR 对同一个信号量进行”give”操作，使得延迟处理任务解除阻塞，从而事件在延迟处理任务中得到相应的处理。

在这种中断同步的情形下，信号量可以看作是一个深度为 1 的队列。这个队列由于最多只能保存一个数据单元，所以其不为空则为满(所谓”二值”)。延迟处理任务调用xSemaphoreTake()时，等效于带阻塞时间地读取队列，如果队列为空的话任务则进入阻塞态。当事件发生后，ISR 简单地通过调用xSemaphoreGiveFromISR()放置一个令牌(信号量)到队列中，使得队列成为满状态。这也使得延迟处理任务切出阻塞态，并移除令牌，使得队列再次成为空。当任务完成处理后，再次读取队列，发现队列为空，又进入阻塞态，等待下一次事件发生。

**vSemaphoreCreateBinary() API 函数**

FreeRTOS 中各种信号量的句柄都存储在 xSemaphoreHandle 类型的变量中。在使用信号量之前 ， 必 须 先 创 建 它 。 创 建 二 值 信 号 量 使 用vSemaphoreCreateBinary()API 函数

```c
void vSemaphoreCreateBinary( xSemaphoreHandle xSemaphore );
```

**表  vSemaphoreCreateBinary()参数**

| 参数名     | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| xSemaphore | 创建的信号量<br/>需要说明的是 vSemaphoreCreateBinary()在实现上是一个宏，所以<br/>信号量变量应当直接传入，而不是传址。本章中包含本函数调用的示<br/>例可用于参考进行复制。 |

**xSemaphoreTake() API 函数**

“带走(Taking)”一个信号量意为”获取(Obtain)”或”接收(Receive)”信号量。只有当信号量有效的时候才可以被获取。
除互斥信号量(Recursive Semaphore，直译为递归信号量，按通常的说法译为互斥信号量)外，所有类型的信号量都可以调用函数 xSemaphoreTake()来获取。

**但 xSemaphoreTake()不能在中断服务例程中调用。**

```c
portBASE_TYPE xSemaphoreTake( xSemaphoreHandle xSemaphore, portTickType                                           xTicksToWait );
```

**表  xSemaphoreTake()参数及返回值**

| 参数名       | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| xSemaphore   | 获取得到的信号量<br/>信号量由定义为 xSemaphoreHandle 类型的变量引用。信号量在使用前必须先创建。 |
| xTicksToWait | 阻塞超时时间。任务进入阻塞态以等待信号量有效的最长时间。<br/>如果 xTicksToWait 为 0，则 xSemaphoreTake()在信号量无效时会立即返回。<br/>阻塞时间是以系统心跳周期为单位的，所以绝对时间取决于系统心跳频率。常量 portTICK_RATE_MS 可以用来把心跳时间单位转换为毫秒时间单位。<br/>如 果 把 xTicksToWait 设 置 为 portMAX_DELAY ， 并 且 在FreeRTOSConig.h 中设定 INCLUDE_vTaskSuspend 为 1，那么阻塞等待将没有超时限制。 |
| 返回值       | 有两个可能的返回值:<br/>1. pdPASS<br/>只有一种情况会返回 pdPASS，那就是成功获得信号量。<br/>如果设定了阻塞超时时间(xTicksToWait 非 0)，在函数返回之前任务将被转移到阻塞态以等待信号量有效。如果在超时到来前信号量变为有效，亦可被成功获取，返回 pdPASS。<br/>2. pdFALSE<br/>未能获得信号量。<br/>如果设定了阻塞超时时间（xTicksToWait 非 0），在函数返回之前任务将被转移到阻塞态以等待信号量有效。但直到超时信号量也没有变为有效，所以不会获得信号量，返回 pdFALSE。 |

**xSemaphoreGiveFromISR() API 函数**

除 互 斥 信 号 量 外 ， FreeRTOS 支 持 的 其 它 类 型 的 信 号 量 都 可 以 通 过 调 用xSemaphoreGiveFromISR()给出。
xSemaphoreGiveFromISR()是 xSemaphoreGive()的特殊形式，专门用于中断服务例程中。

```c
portBASE_TYPE xSemaphoreGiveFromISR( xSemaphoreHandle xSemaphore,
                                       portBASE_TYPE *pxHigherPriorityTaskWoken );
```

**表  xSemaphoreGiveFromISR()参数与返回值**

| 参数名                    | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| xSemaphore                | 给出的信号量<br/>信号量由定义为 xSemaphoreHandle 类型的变量引用。<br/>信号量在使用前必须先创建。 |
| pxHigherPriorityTaskWoken | 对某个信号量而言，可能有不止一个任务处于阻塞态在等待其有效。调用 xSemaphoreGiveFromISR()会让信号量变为有效，所以会让其中一个等待任务切出阻塞态。如果调用 xSemaphoreGiveFromISR()使得一个任务解除阻塞，并且这个任务的优先级高于当前任务(也就是被中断的任务)，那么 xSemaphoreGiveFromISR()会在 函 数 内 部 将*pxHigherPriorityTaskWoken 设 为pdTRUE。<br/>如 果xSemaphoreGiveFromISR() 将 此 值 设 为pdTRUE，则在中断退出前应当进行一次上下文切换。这样才能保证中断直接返回到就绪态任务中优先级最高的任务中。 |
| 返回值                    | 有两个可能的返回值:<br/>1. pdPASS<br/>xSemaphoreGiveFromISR()调用成功。<br/>2. pdFAIL<br/>如果信号量已经有效，无法给出，则返回 pdFAIL。 |

例3.1 利用二值信号量对任务和中断进行同步

```c
//周期性产生软件中断的周期任务实现代码

static void vPeriodicTask( void *pvParameters )
{
	for( ;; )
	{
/* 此任务通过每500毫秒产生一个软件中断来”模拟”中断事件 */
		vTaskDelay( 500 / portTICK_RATE_MS );
/* 产生中断，并在产生之前和之后输出信息，以便在执行结果中直观直出执行流程 */
		vPrintString( "Periodic task - About to generate an interrupt.\r\n" );
		__asm{ int 0x82 } /* 这条语句产生中断 */
		vPrintString( "Periodic task - Interrupt generated.\r\n\r\n\r\n" );
	}
}

//中延迟处理任务的实现代码(此任务与中断同步)

static void vHandlerTask( void *pvParameters )
{
/* As per most tasks, this task is implemented within an infinite loop. */
	for( ;; )
	{
/* 使用信号量等待一个事件。信号量在调度器启动之前，也即此任务执行之前就已被创建。任务被无超
时阻塞，所以此函数调用也只会在成功获取信号量之后才会返回。此处也没有必要检测返回值 */
		xSemaphoreTake( xBinarySemaphore, portMAX_DELAY );
/* 程序运行到这里时，事件必然已经发生。本例的事件处理只是简单地打印输出一个信息 */
		vPrintString( "Handler task - Processing event.\r\n" );
	}
}

//软件中断的中断服务例程

static void __interrupt __far vExampleInterruptHandler( void )
{
	static portBASE_TYPE xHigherPriorityTaskWoken;
	xHigherPriorityTaskWoken = pdFALSE;
/* 'Give' the semaphore to unblock the task. */
	xSemaphoreGiveFromISR( xBinarySemaphore, &xHigherPriorityTaskWoken );
	if( xHigherPriorityTaskWoken == pdTRUE )
	{
/* 给出信号量以使得等待此信号量的任务解除阻塞。如果解出阻塞的任务的优先级高于当前任务的优先
级 – 强制进行一次任务切换，以确保中断直接返回到解出阻塞的任务(优选级更高)。
说明：在实际使用中，ISR中强制上下文切换的宏依赖于具体移植。此处调用的是基于Open Watcom DOS
移植的宏。其它平台下的移植可能有不同的语法要求。对于实际使用的平台，请参如数对应移植自带的示
例程序，以决定正确的语法和符号。
*/
		portSWITCH_CONTEXT();
	}
}

//main()函数实现代码

int main( void )
{
/* 信号量在使用前都必须先创建。本例中创建了一个二值信号量 */
	vSemaphoreCreateBinary( xBinarySemaphore );
/* 安装中断服务例程 */
	_dos_setvect( 0x82, vExampleInterruptHandler );
/* 检查信号量是否成功创建 */
	if( xBinarySemaphore != NULL )
	{
/* 创建延迟处理任务。此任务将与中断同步。延迟处理任务在创建时使用了一个较高的优先级，以保证
中断退出后会被立即执行。在本例中，为延迟处理任务赋予优先级3 */
		xTaskCreate( vHandlerTask, "Handler", 1000, NULL, 3, NULL );
/* 创建一个任务用于周期性产生软件中断。此任务的优先级低于延迟处理任务。每当延迟处理任务切出
阻塞态，就会抢占周期任务*/
		xTaskCreate( vPeriodicTask, "Periodic", 1000, NULL, 1, NULL );
/* Start the scheduler so the created tasks start executing. */
		vTaskStartScheduler();
	}
/* 如果一切正常，main()函数不会执行到这里，因为调度器已经开始运行任务。但如果程序运行到了这里，
很可能是由于系统内存不足而无法创建空闲任务。第五章会提供更多关于内存管理的信息 */
	for( ;; );
}
```

### 3.2 计数信号量

一个二值信号量被用于让任务和中断进行同步。整个执行流程可以描述为:
1. 中断产生。
2. 中断服务例程启动，给出信号量以使延迟处理任务解除阻塞。
3. 当中断服务例程退出时，延迟处理任务得到执行。延迟处理任务做的第一件事便是
获取信号量。
4. 延迟处理任务完成中断事件处理后，试图再次获取信号量——如果此时信号量无效，
任务将切入阻塞待等待事件发生。

在中断以相对较慢的频率发生的情况下，上面描述的流程是足够而完美的。如果在延迟处理任务完成上一个中断事件的处理之前，新的中断事件又发生了，等效于将新的事件锁存在二值信号量中，使得延迟处理任务在处理完上一个事件之后，立即就可以处理新的事件。也就是说，延迟处理任务在两次事件处理之间，不会有进入阻塞态的机会，因为信号量中锁存有一个事件，所以当 xSempaphoreTake()调用时，信号量立即有效。

一个二值信号量最多只可以锁存一个中断事件。在锁存的事件还未被处理之前，如果还有中断事件发生，那么后续发生的中断事件将会丢失。如果用计数信号量代替二值信号量，那么，这种丢中断的情形将可以避免。

**计数信号量两种经典用法**

1. 事件计数

   在这种用法中，每次事件发生时，中断服务例程都会“给出(Give)”信号量——信号量在每次被给出时其计数值加 1。延迟处理任务每处理一个任务都会”获取(Take)”一次信号量——信号量在每次被获取时其计数值减 1。信号量的计数值其实就是已发生事件的数目与已处理事件的数目之间的差值。

   用于事件计数的计数信号量，在被创建时其计数值被初始化为 0。

2. 资源管理

   在这种用法中，信号量的计数值用于表示可用资源的数目。一个任务要获取资源的控制权，其必须先获得信号量——使信号量的计数值减 1。当计数值减至 0，则表示没有可用资源。当任务利用资源完成工作后，将给出(归还)信号量——使信号量的计数值加 1。

   用于资源管理的信号量，在创建时其计数值被初始化为可用资源总数。

**xSemaphoreCreateCounting() API 函数**

FreeRTOS 中所有种类的信号量句柄都由声明为 xSemaphoreHandle 类型的变量保存。
信号量在使用前必须先被创建。使用 xSemaphoreCreateCounting() API 函数来创建一个计数信号量。

```c
xSemaphoreHandle xSemaphoreCreateCounting( unsigned portBASE_TYPE uxMaxCount,
                                          unsigned portBASE_TYPE uxInitialCount );
```

**表 xSemaphoreCreateCounting()参数与返回值**

| 参数名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| uxMaxCount     | 最大计数值。如果把计数信号量类比于队列的话，uxMaxCount 值就是队列的最大深度。<br/>当此信号量用于对事件计数或锁存事件的话，uxMaxCount 就是可锁存事件的最大数目。<br/>当此信号量用于对一组资源的访问进行管理的话，uxMaxCount 应当设置为所有可用资源的总数。 |
| uxInitialCount | 信号量的初始计数值。<br/>当此信号量用于事件计数的话，uxInitialCount 应当设置为 0——因为当信号量被创建时，还没有事件发生。<br/>当 此 信 号 量 用 于 资 源 管 理 的 话 ， uxInitialCount 应 当 等 于uxMaxCount——因为当信号量被创建时，所有的资源都是可用的。 |

例3.2 利用计数信号量对任务和中断进行同步

例3.2 用计数信号量代替二值信号量对例3.1 的实现进行了改进。修改 main()函数，调用xSemaphoreCreateCounting()，以代替对 xSemaphoreCreateBinary()的调用。

```c
/* 在信号量使用之前必须先创建。本例中创建了一个计数信号量。此信号量的最大计数值为10，初始计数值为0 */
xCountingSemaphore = xSemaphoreCreateCounting( 10, 0 );
```

```c
//中断服务例程实现代码

static void __interrupt __far vExampleInterruptHandler( void )
{
	static portBASE_TYPE xHigherPriorityTaskWoken;
	xHigherPriorityTaskWoken = pdFALSE;
/* 多次给出信号量。第一次给出时使得延迟处理任务解除阻塞。后续给出用于演示利用被信号量锁存事件，
以便延迟处理任何依序对这些中断事件进行处理而不会丢中断。用这种方式来模拟处理器产生多个中断，尽管
这些事件只是在单次中断中模拟出来的 */
	xSemaphoreGiveFromISR( xCountingSemaphore, &xHigherPriorityTaskWoken );
	xSemaphoreGiveFromISR( xCountingSemaphore, &xHigherPriorityTaskWoken );
	xSemaphoreGiveFromISR( xCountingSemaphore, &xHigherPriorityTaskWoken );
	if( xHigherPriorityTaskWoken == pdTRUE )
	{
/* 给出信号量以使得等待此信号量的任务解除阻塞。如果解出阻塞的任务的优先级高于当前任务的优先
级 – 强制进行一次任务切换，以确保中断直接返回到解出阻塞的任务(优选级更高)。

说明：在实际使用中，ISR中强制上下文切换的宏依赖于具体移植。此处调用的是基于Open Watcom DOS
移植的宏。其它平台下的移植可能有不同的语法要求。对于实际使用的平台，请参如数对应移植自带的示
例程序，以决定正确的语法和符号。
*/
		portSWITCH_CONTEXT();
	}
}

```

其它函数都复用例3.1中的代码，保持不变。

### 3.3 在中断服务例程中使用队列

xQueueSendToFrontFromISR()， xQueueSendToBackFromISR()与 xQueueReceiveFromISR()分别是 xQueueSendToFront()， xQueueSendToBack()与 xQueueReceive()的中断安全版本，专门用于中断服务例程中。信号量用于事件通信。而队列不仅可以用于事件通信，还可以用来传递数据。

**xQueueSendToFrontFromISR()与 xQueueSendToBackFromISR() API 函数**

说明：xQueueSendFromISR()完全等同于 xQueueSendToBackFromISR()。

```c
portBASE_TYPE xQueueSendToFrontFromISR( xQueueHandle xQueue,void *pvItemToQueue
                                      portBASE_TYPE *pxHigherPriorityTaskWoken );

portBASE_TYPE xQueueSendToBackFromISR( xQueueHandle xQueue,void *pvItemToQueue
                                       portBASE_TYPE *pxHigherPriorityTaskWoken);
```

**表  xQueueSendToFrontFromISR 与 xQueueSendToBackFromISR()参数与返回值**

| 参数名                    | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| xQueue                    | 目标队列的句柄。这个句柄即是调用 xQueueCreate()创建该队列时的返回值。 |
| pvItemToQueue             | 发送数据的指针。其指向将要复制到目标队列中的数据单元。<br/>由于在创建队列时设置了队列中数据单元的长度，所以会从该指针指向的空间复制对应长度的数据到队列的存储区域。 |
| pxHigherPriorityTaskWoken | 对某个队列而言，可能有不止一个任务处于阻塞态在等待其数据有效。调用 xQueueSendToFrontFromISR()或 xQueueSendToBackFromISR()会使得队列数据变为有效，所以会让其中一个等待任务切出阻塞态。如果调用这两个 API 函数使得一个任务解除阻塞，并且这个任务的优先级高于当前任务(也就是被中断的任务)，那么 API 会在函数内部将*pxHigherPriorityTaskWoken 设为 pdTRUE。<br/>如果这两个 API 函数将此值设为 pdTRUE，则在中断退出前应当进行一次上下文切换。这样才能保证中断直接返回到就绪态任务中优先级最高的任务中。 |
| 返回值                    | 有两个可能的返回值:<br/>1. pdPASS<br/>返回 pdPASS 只会有一种情况，那就是数据被成功发送<br/>到队列中。<br/>2. errQUEUE_FULL<br/>如果由于队列已满而无法将数据写入，则将返回<br/>errQUEUE_FULL。 |

例 3.3 利用队列在中断服务中发送或接收数据

```c
//创建一个周期任务用于每 200 毫秒往队列中发送五个数值，五个数值都发送完后便产生一个软件中断。
static void vIntegerGenerator( void *pvParameters )
{
	portTickType xLastExecutionTime;
	unsigned portLONG ulValueToSend = 0;
	int i;
/* 初始化变量，用于调用 vTaskDelayUntil(). */
	xLastExecutionTime = xTaskGetTickCount();
	for( ;; )
	{
/* 这是个周期性任务。进入阻塞态，直到该再次运行的时刻。此任务每200毫秒执行一次 */
		vTaskDelayUntil( &xLastExecutionTime, 200 / portTICK_RATE_MS );
/* 连续五次发送递增数值到队列。这此数值将在中断服务例程中读出。中断服务例程会将队列读空，所
以此任务可以确保将所有的数值都发送到队列。因此不需要指定阻塞超时时间 */
		for( i = 0; i < 5; i++ )
		{
			xQueueSendToBack( xIntegerQueue, &ulValueToSend, 0 );
			ulValueToSend++;
		}
/* 产生中断，以让中断服务例程读取队列 */
		vPrintString( "Generator task - About to generate an interrupt.\r\n" );
		__asm{ int 0x82 } /* This line generates the interrupt. */
		vPrintString( "Generator task - Interrupt generated.\r\n\r\n\r\n" );
	}
}


//中断服务例程重复调用 xQueueReceiveFromISR()，直到被周期任务写到队列的数值都被读出，以将队列读空。每个接收到的数值的低两位用于一个字符串数组的索引，被索引到的字符串的指针将通过调用 xQueueSendFromISR()发送到另一个队列中。
static void __interrupt __far vExampleInterruptHandler( void )
{
	static portBASE_TYPE xHigherPriorityTaskWoken;
	static unsigned long ulReceivedNumber;
/* 这些字符串被声明为static const，以保证它们不会被定位到ISR的栈空间中，即使ISR没有运行它们也是存
在的 */
	static const char *pcStrings[] =
	{
		"String 0\r\n",
		"String 1\r\n",
		"String 2\r\n",
		"String 3\r\n"
	};
	xHigherPriorityTaskWoken = pdFALSE;
/* 重复执行，直到队列为空 */
	while( xQueueReceiveFromISR( xIntegerQueue,
	&ulReceivedNumber,
	&xHigherPriorityTaskWoken ) != errQUEUE_EMPTY )
	{
/* 截断收到的数据，保留低两位(数值范围0到3).然后将索引到的字符串指针发送到另一个队列 */
		ulReceivedNumber &= 0x03;
		xQueueSendToBackFromISR( xStringQueue,
		&pcStrings[ ulReceivedNumber ],
		&xHigherPriorityTaskWoken );
	}
/* 被队列读写操作解除阻塞的任务，其优先级是否高于当前任务？如果是，则进行任务上下文切换 */
	if( xHigherPriorityTaskWoken == pdTRUE )
	{
/* 说明：在实际使用中，ISR中强制上下文切换的宏依赖于具体移植。此处调用的是基于Open Watcom
DOS移植的宏。其它平台下的移植可能有不同的语法要求。对于实际使用的平台，请参如数对应移植自带
的示例程序，以决定正确的语法和符号。 */
		portSWITCH_CONTEXT();
	}
}

//另一个任务将接收从中断服务例程发出的字符串指针。此任务在读队列时被阻塞，直到队列中有消息到来，并将接收到的字符串打印输出。
static void vStringPrinter( void *pvParameters )
{
	char *pcString;
	for( ;; )
	{
	/* Block on the queue to wait for data to arrive. */
		xQueueReceive( xStringQueue, &pcString, portMAX_DELAY );
	/* Print out the string received. */
		vPrintString( pcString );
	}
}

//main()函数创建队列和任务，然后启动调度器。
int main( void )
{
/* 队列使用前必须先创建。本例中创建了两个队列。一个队列用于保存类型为unsigned long的变量，另一
个队列用于保存类型为char*的变量。两个队列的深度都为10。在实际应用中应当检测返回值以确保队列创建
成功 */
	xIntegerQueue = xQueueCreate( 10, sizeof( unsigned long ) );
	xStringQueue = xQueueCreate( 10, sizeof( char * ) );
/* 安装中断服务例程。 */
	_dos_setvect( 0x82, vExampleInterruptHandler );
/* 创建任务用于往中断服务例程中发送数值。此任务优先级为1 */
	xTaskCreate( vIntegerGenerator, "IntGen", 1000, NULL, 1, NULL );
/* 创建任务用于从中断服务例程中接收字符串，并打印输出。此任务优先级为2 */
	xTaskCreate( vStringPrinter, "String", 1000, NULL, 2, NULL );
/* Start the scheduler so the created tasks start executing. */
	vTaskStartScheduler();
/* 如果一切正常，main()函数不会执行到这里，因为调度器已经开始运行任务。但如果程序运行到了这里，
很可能是由于系统内存不足而无法创建空闲任务。第五章会提供更多关于内存管理的信息 */
	for( ;; );
}
```

## 第四章 资源管理

多任务系统中存在一种潜在的风险。当一个任务在使用某个资源的过程中，即还没有完全结束对资源的访问时，便被切出运行态，使得资源处于非一致，不完整的状态。如果这个时候有另一个任务或者中断来访问这个资源，则会导致数据损坏或是其它相似的错误。

以下便是一些例子：

1. 访问外设
  考虑如下情形，有两个任务都试图往一个 LCD 中写数据：

  任务 A 运行，并往 LCD 写字符串”Hello world”。
  任务 A 被任务 B 抢占，但此时字符串才输出到”Hello w”。
  任务 B 往 LCD 写”Abort, Retry, Fail?”，然后进入阻塞态。
  任务 A 从被抢占处继续执行，完成剩余的字符输出——“orld”。
  现在 LCD 显示的是被破坏了的字符串”Hello wAbort, Retry, Fail?orld”。

2. 读-改-写操作
  下面是一段 C 代码和其等效的 ARM7 汇编代码。可以看出， PORTA中的值先从内存读到寄存器，在寄存器中完成修改，然后再写回内存。这所是所谓的读-改-写操作。

  ```c
  /* The C code being compiled. */
  155: PORTA |= 0x01;
  /* The assembly code produced. */
  0x00000264 481C LDR R0,[PC,#0x0070] ; Obtain the address of PORTA
  0x00000266 6801 LDR R1,[R0,#0x00]; Read the value of PORTA into R1
  0x00000268 2201 MOV R2,#0x01 ; Move the absolute constant 1 into R2
  0x0000026A 4311 ORR R1,R2 ; OR R1 (PORTA) with R2 (constant 1)
  0x0000026C 6001 STR R1,[R0,#0x00] ; Store the new value back to PORTA
  ```

  这是一个”非原子”操作，因为完成整个操作需要不止一条指令，所以操作过程可能被中断。考虑如下情形，两个任务都试图更新一个名为 PORTA 的内存映射寄存器：
  任务 A 把 PORTA 的值加载到寄存器中——整个流程的读操作。
  在任务 A 完成整个流程的改和写操作之前，被任务 B 抢占。
  任务 B 完整的执行了对 PORTA 的更新流程，然后进入阻塞态。
  任务 A 从被抢占处继续执行。其修改了一个 PORTA 的拷贝，这其实只是寄存器在任务 A 回写到 PORTA 之前曾经保存过的值。
  任务 A 更新并回写了一个过期的 PORTA 寄存器值。在任务 A 获得拷贝与更新回写之间，任务 B 又修改了 PORTA 的值。而之后任务 A 对 PORTA 的回写操作，覆盖了任务 B 对 PORTA 进行的修改结果，效果上等同于破坏了 PORTA 寄存器的值。虽然是以一个外围设备寄存器为例，但是整个情形同样适用于全局变量的读-改-写操作。

3. 变量的非原子访问

   更新结构体的多个成员变量，或是更新的变量其长度超过了架构体系的自然长度(比如，更新一个 16 位机上的 32 位变量)均是非原子操作的例子。如果这样的操作被中断，将可能导致数据损坏或丢失。

4. 函数重入

   如果一个函数可以安全地被多个任务调用，或是在任务与中断中均可调用，则这个函数是可重入的。
   每个任务都单独维护自己的栈空间及其自身在的内存寄存器组中的值。如果一个函数除了访问自己栈空间上分配的数据或是内核寄存器中的数据外，不会访问其它任何数据，则这个函数就是可重入的。

**互斥**

访问一个被多任务共享，或是被任务与中断共享的资源时，需要采用”互斥”技术以保证数据在任何时候都保持一致性。这样做的目的是要确保任务从开始访问资源就具有排它性，直至这个资源又恢复到完整状态。FreeRTOS 提供了多种特性用以实现互斥，但是最好的互斥方法（如果可能的话，任何时候都当如此）还是通过精心设计应用程序，尽量不要共享资源，或者是每个资源都通过单任务访问。

### 4.1 临界区与挂起调度器

**基本临界区**

基本临界区是指宏 taskENTER_CRITICAL()与 taskEXIT_CRITICAL()之间的代码区间，下面是一段范例代码。Critical Sections 也被称作 Critical Regions。

```c
/* 为了保证对PORTA寄存器的访问不被中断，将访问操作放入临界区。
进入临界区 */
taskENTER_CRITICAL();
/* 在taskENTER_CRITICAL() 与 taskEXIT_CRITICAL()之间不会切换到其它任务。 中断可以执行，也允许
嵌套，但只是针对优先级高于configMAX_SYSCALL_INTERRUPT_PRIORITY的中断 – 而且这些中断不允许访问
FreeRTOS API 函数. */
PORTA |= 0x01;
/* 我们已经完成了对PORTA的访问，因此可以安全地离开临界区了。 */
taskEXIT_CRITICAL();
```

vPrintString()被多个不同的任务调用，所以理论上其函数实现中应当使用一个临界区对标准输出进行保护，如下。

```c
void vPrintString( const portCHAR *pcString )
{
/* 往stdout中写字符串，使用临界区这种原始的方法实现互斥。 */
	taskENTER_CRITICAL();
	{
		printf( "%s", pcString );
		fflush( stdout );
	}
	taskEXIT_CRITICAL();
/* 允许按任意键停止应用程序运行。实际的应用程序如果有使用到键值，还需要对键盘输入进行保护。 */
	if( kbhit() )
	{
		vTaskEndScheduler();
	}
}
```



**挂起调度器**

也可以通过挂起调度器来创建临界区。挂起调度器有些时候也被称为锁定调度器。基本临界区保护一段代码区间不被其它任务或中断打断。由挂起调度器实现的临界区只可以保护一段代码区间不被其它任务打断，因为这种方式下，中断是使能的。
如果一个临界区太长而不适合简单地关中断来实现，可以考虑采用挂起调度器的方式。但是唤醒(resuming, or un-suspending)调度器却是一个相对较长的操作。所以评估哪种是最佳方式需要结合实际情况。

**vTaskSuspendAll() API 函数**

```c
void vTaskSuspendAll( void );
```

通过调用 vTaskSuspendAll()来挂起调度器。挂起调度器可以停止上下文切换而不用关中断。如果某个中断在调度器挂起过程中要求进行上下文切换，则个这请求也会被挂起，直到调度器被唤醒后才会得到执行。

在调度器处于挂起状态时，不能调用 FreeRTOS API 函数。

**xTaskResumeAll() API 函数**

```c
portBASE_TYPE xTaskResumeAll( void );
```

| 参数名 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| 返回值 | 在调度器挂起过程中，上下文切换请求也会被挂起，直到调度器被唤醒后才会得到执行。如果一个挂起的上下文切换请求在xTaskResumeAll()返回前得到执行，则函数返回 pdTRUE。在其它情况下，xTaskResumeAll()返回 pdFALSE。 |

嵌套调用 vTaskSuspendAll()和 xTaskResumeAll()是安全的，因为内核有维护一个嵌套深度计数。调度器只会在嵌套深度计数为 0 时才会被唤醒——即在为每个之前调用的 vTaskSuspendAll()都配套调用了 xTaskResumAll()之后。

```c
void vPrintString( const portCHAR *pcString )
{
/* Write the string to stdout, suspending the scheduler as a method
of mutual exclusion. */
	vTaskSuspendScheduler();
	{
		printf( "%s", pcString );
		fflush( stdout );
	}
	xTaskResumeScheduler();
/* Allow any key to stop the application running. A real application that
actually used the key value should protect access to the keyboard input too. */
	if( kbhit() )
	{
		vTaskEndScheduler();
	}
}
```

### 4.2 互斥量(及二值信号量)

互斥量是一种特殊的二值信号量，用于控制在两个或多个任务间访问共享资源。单词MUTEX(互斥量)源于”MUTual EXclusion”。
在用于互斥的场合，互斥量从概念上可看作是与共享资源关联的令牌。一个任务想要合法地访问资源，其必须先成功地得到(Take)该资源对应的令牌(成为令牌持有者)。当令牌持有者完成资源使用，其必须马上归还(Give)令牌。只有归还了令牌，其它任务才可能成功持有，也才可能安全地访问该共享资源。一个任务除非持有了令牌，否则不
允许访问共享资源。
虽然互斥量与二值信号量之间具有很多相同的特性，两者间最大的区别在于信号量在被获得之后所发生的事情：

- 用于互斥的信号量必须归还。
- 用于同步的信号量通常是完成同步之后便丢弃，不再归还。

**xSemaphoreCreateMutex() API 函数**

互斥量是一种信号量。FreeRTOS 中所有种类的信号量句柄都保存在类型为xSemaphoreHandle 的变量中。
互斥量在使用前必须先创建。创建一个互斥量类型的信号量需要使用xSemaphoreCreateMutex() API 函数。

```c
xSemaphoreHandle xSemaphoreCreateMutex( void );
```

**表 xSemaphoreCreateMutex()返回值**

| 参数名 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| 返回值 | 如果返回 NULL 表示互斥量创建失败。原因是内存堆空间不足导致FreeRTOS 无法为互斥量分配结构数据空间。第五章提供更多关于内存管理方面的信息。<br/>返回非 NULL 值表示互斥量创建成功。返回值应当保存起来作为该互斥量的句柄。 |

例 使用信号量重写vPrintString()

```c
static void prvNewPrintString( const portCHAR *pcString )
{
/* 互斥量在调度器启动之前就已创建，所以在此任务运行时信号量就已经存在了。
试图获得互斥量。如果互斥量无效，则将阻塞，进入无超时等待。xSemaphoreTake()只可能在成功获得互
斥量后返回，所以无需检测返回值。如果指定了等待超时时间，则代码必须检测到xSemaphoreTake()返回
pdTRUE后，才能访问共享资源(此处是指标准输出)。 */
	xSemaphoreTake( xMutex, portMAX_DELAY );
	{
/* 程序执行到这里表示已经成功持有互斥量。现在可以自由访问标准输出，因为任意时刻只会有一个任
务能持有互斥量。 */
		printf( "%s", pcString );
		fflush( stdout );
/* 互斥量必须归还! */
	}
	xSemaphoreGive( xMutex );
/* Allow any key to stop the application running. A real application that
actually used the key value should protect access to the keyboard too. A
real application is very unlikely to have more than one task processing
key presses though! */
	if( kbhit() )
	{
		vTaskEndScheduler();
	}
}

int main( void )
{
/* 信号量使用前必须先创建。本例创建了一个互斥量类型的信号量。 */
	xMutex = xSemaphoreCreateMutex();
/* 本例中的任务会使用一个随机延迟时间，这里给随机数发生器生成种子。 */
	srand( 567 );
/* Check the semaphore was created successfully before creating the tasks. */
	if( xMutex != NULL )
	{
/* Create two instances of the tasks that write to stdout. The string
they write is passed in as the task parameter. The tasks are created
at different priorities so some pre-emption will occur. */
		xTaskCreate( prvPrintTask, "Print1", 1000,
"Task 1 ******************************************\r\n", 1, NULL );
		xTaskCreate( prvPrintTask, "Print2", 1000,
"Task 2 ------------------------------------------\r\n", 2, NULL );
/* Start the scheduler so the created tasks start executing. */
	vTaskStartScheduler();
	}
/* 如果一切正常，main()函数不会执行到这里，因为调度器已经开始运行任务。但如果程序运行到了这里，
很可能是由于系统内存不足而无法创建空闲任务。第五章会提供更多关于内存管理的信息 */
	for( ;; );
}
```

**优先级反转**

下图中也展现出了采用互斥量提供互斥功能的潜在缺陷之一。在这种可能的执行流程描述中，高优先级的任务 2 竟然必须等待低优先级的任务 1 放弃对互斥量的持有权。高优先级任务被低优先级任务阻塞推迟的行为被称为”优先级反转”。这是一种不合理的行为方式，如果把这种行为再进一步放大，当高优先级任务正等待信号量的时候，一个介于两个任务优先之间的中等优先级任务开始执行——这就会导致一个高优先级任务在等待一个低优先级任务，而低优先级任务却无法执行！这种最坏的情形在图中进行展示。

![](/home/sunny/Nutstore Files/我的坚果云/TyporaFile/freeRTOS/优先级反转.png)

**优先级继承**

FreeRTOS 中互斥量与二值信号量十分相似——唯一的区别就是互斥量自动提供了一个基本的”优先级继承”机制。优先级继承是最小化优先级反转负面影响的一种方案——其并不能修正优先级反转带来的问题，仅仅是减小优先级反转的影响。优先级继承使得系统行为的数学分析更为复杂，所以如果可以避免的话，并不建议系统实现对优先
级继承有所依赖。
优先级继承暂时地将互斥量持有者的优先级提升至所有等待此互斥量的任务所具有的最高优先级。持有互斥量的低优先级任务”继承”了等待互斥量的任务的优先级。这种机制在下图中进行展示。互斥量持有者在归还互斥量时，优先级会自动设置为其原来的优先级。

![](/home/sunny/Nutstore Files/我的坚果云/TyporaFile/freeRTOS/优先级继承.png)

**死锁**

死锁是利用互斥量提供互斥功能的另一个潜在缺陷。Deadlock 有时候会被更戏剧
性地称为”deadly embrace(抱死)”。
当两个任务都在等待被对方持有的资源时，两个任务都无法再继续执行，这种情况
就被称为死锁。考虑如下情形，任务 A 与任务 B 都需要获得互斥量 X 与互斥量 Y 以完
成各自的工作：

1. 任务 A 执行，并成功获得了互斥量 X。
2. 任务 A 被任务 B 抢占。
3. 任务 B 成功获得了互斥量 Y，之后又试图获取互斥量 X——但互斥量 X 已
经被任务 A 持有，所以对任务 B 无效。任务 B 选择进入阻塞态以等待互斥
量 X 被释放。
4. 任务 A 得以继续执行。其试图获取互斥量 Y——但互斥量 Y 已经被任务 B
持有而对任务 A 无效。任务 A 也选择进入阻塞态以等待互斥量 Y 被释放。

这种情形的最终结局是，任务 A 在等待一个被任务 B 持有的互斥量，而任务 B 也在等待一个被任务 A 持有的互斥量。死锁于是发生，因为两个任务都不可能再执行下去了。
和优先级反转一样，避免死锁的最好方法就是在设计阶段就考虑到这种潜在风险，这样设计出来的系统就不应该会出现死锁的情况。于实践经验而言，对于一个小型嵌入式系统，死锁并不是一个大问题，因为系统设计者对整个应用程序都非常清楚，所以能够找出发生死锁的代码区域，并消除死锁问题。

### 4.3 守护任务

守护任务提供了一种干净利落的方法来实现互斥功能，而不用担心会发生优先级反转和死锁。
守护任务是对某个资源具有唯一所有权的任务。只有守护任务才可以直接访问其守护的资源——其它任务要访问该资源只能间接地通过守护任务提供的服务。

## 第五章 内存管理

每当任务，队列或是信号量被创建时，内核需要进行动态内存分配。虽然可以调用标准的 malloc()与 free()库函数，但必须承担以下若干问题：

1. 这两个函数在小型嵌入式系统中可能不可用。
2. 这两个函数的具体实现可能会相对较大，会占用较多宝贵的代码空间。
3. 这两个函数通常不具备线程安全特性。
4. 这两个函数具有不确定性。每次调用时的时间开销都可能不同。
5. 这两个函数会产生内存碎片。
6. 这两个函数会使得链接器配置得复杂。

不同的嵌入式系统具有不同的内存配置和时间要求。所以单一的内存分配算法只可能适合部分应用程序。因此，FreeRTOS 将内存分配作为可移植层面(相对于基本的内核代码部分而言)。这使得不同的应用程序可以提供适合自身的具体实现。

当内核请求内存时，其调用 pvPortMalloc()而不是直接调用 malloc()；当释放内存时，调用 vPortFree()而不是直接调用 free()。pvPortMalloc()具有与 malloc()相同的函数原型；vPortFree()也具有与 free()相同的函数原型。
FreeRTOS 自带有三种 pvPortMalloc()与 vPortFree()实现范例，这三种方式都会在本章描述。FreeRTOS 的用户可以选用其中一种，也可以采用自己的内存管理方式。这三个范例对应三个源文件：heap_1.c，heap_2.c，heap_3.c——这三个文件都放在目录 FreeRTOS\Source\Portable\MemMang 中。早期版本的 FreeRTOS 所采用的原始内存池和内存块分配方案已经被移除了，因为定义内存块和内存池的大小需要较深入的努力和理解。

在小型嵌入式系统中，通常是在启动调度器之前创建任务，队列和信号量。这种情况表明，动态分配内存只会出现在应用程序真正开始执行实时功能之前，而且内存一旦分配就不会再释放。这就意味着选择内存分配方案时不必考虑一些复杂的因素，比如确定性与内存碎片等，而只需要从性能上考虑，比如代码大小和简易性。
