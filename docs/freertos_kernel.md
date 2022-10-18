---
layout: default
title: Using Checked C version of FreeRTOS Kernel
nav_order: 6
---

With the help of 3C, we converted the FreeRTOS Kernel into Checked C.
You can use this version of kernel in your project instead of the C version.

The code for converted version can be found [here](https://github.com/3clsp/FreeRTOS_Kernel/tree/main/Fixed/Source).
The following steps assume you have Checked C version of clang in your system.

## Compiling the kernel

- Update the `Makefile` with the path to `FreeRTOS` folder. This is needed if `projCOVERAGE_TEST` is set to 0 since one of the trace header files have to be modified to work with our kernel. You can find this modified version [here](https://github.com/3clsp/FreeRTOS_Kernel/blob/main/Fixed/FreeRTOS-Plus/Source/FreeRTOS-Plus-Trace/Include/trcKernelPort.h).
    ```make
    FREERTOS_ROOT := /path/to/FreeRTOS
    FREERTOS_DIR := $(abspath $(FREERTOS_ROOT))/FreeRTOS
    FREERTOS_PLUS_DIR := $(abspath $(FREERTOS_ROOT))/FreeRTOS-Plus
    ```

- Include the config file required for your application. The following folder contains the config file for the Posix_GCC demo application.
    ```make
    INCLUDE_DIRS += -I$(FREERTOS_DIR)/Demo/Posix_GCC
    ```

- Now you can compile the kernel with `make`.


## Using the kernel

One thing to keep in mind is that your application should be compiled using the modified [FreeRTOS.h](https://github.com/3clsp/FreeRTOS_Kernel/blob/main/Fixed/Source/include/FreeRTOS.h). This means that the application have to be compiled using the Checked C version of clang.

Modify the `Makefile` of your application so that it stops after all the object files are created. You can also remove the kernel file compilation steps from the `Makefile` since we already have the Checked C version object files with us.

To get the final application, use the Checked C kernel object files during the final linking step. The following is the command for Posix_GCC demo application.

```sh
$ gcc code_coverage_additions.o console.o main_blinky.o main.o main_full.o run-time-stats-utils.o croutine.o event_groups.o list.o queue.o stream_buffer.o tasks.o timers.o heap_3.o wait_for_event.o port.o AbortDelay.o BlockQ.o blocktim.o countsem.o death.o dynamic.o EventGroupsDemo.o flop.o GenQTest.o integer.o IntSemTest.o MessageBufferAMP.o MessageBufferDemo.o PollQ.o QPeek.o QueueOverwrite.o QueueSet.o QueueSetPolling.o recmutex.o semtest.o StaticAllocation.o StreamBufferDemo.o StreamBufferInterrupt.o TaskNotify.o TimerDemo.o trcKernelPort.o trcSnapshotRecorder.o trcStreamingRecorder.o trcStreamingPort.o -ggdb3 -pthread -O3 -o build/posix_demo
```

Path to object files are emitted in the above command to make it more readable.

