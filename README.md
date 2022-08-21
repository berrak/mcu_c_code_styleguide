## My embedded coding and naming style guide

### Table of contents

    - [ File naming style ]
    - [ Header include files ]
    - [ Always use <stdint.h> defined integer types ]
    - [ Hardware register files ]
    - [ Sample snippet from a hardware file ]
    - [ C or C++ in embedded programming ]
    - [ Names used for C++ class and functions/methods ]
    - [ Structures, enums and typedef ]
    - [ Variable names ]

### File naming style

**Files are named** all `lower case`, without any hyphen '`-`' or whitespace, i.e., preferable all in one world. That ensures that used tools will not have any problem reading files or paths regardless of the host operating system or build tools used. You may use '`_`' though.

```
syscalls.c          // OK
sys_calls.c         // OK
sys calls.c         // Wrong
SysCalls.c          // Wrong
```

Avoid building issues with naming file system directories in the same manner, which are part of the toolchain or source tree. Other directories can be named freely, with hyphens, camel-case, or all caps.

### Header include files

Always include guard defines to prevent multiple inclusions. For example, use `#ifndef` and name it based on the file name (`basictimers.h`). Use upper case letters with '`_`' replacing the dot. Begin and end the `#define` with one `_`.

```c
#ifndef _BASICTIMERS_H_
#define _BASICTIMERS_H_
...
#endif /* _BASICTIMERS_H_ */
```

Always include a C++ check and have the external header files outside this C++ check. Further, include any external C standard library header files before any user header files. Finally, every `#define` must be within C++ check brackets. 

```c
...
#include <stdint.h>
#include "f407hardwarebasictimers.h"

#ifdef __cplusplus
 extern "C" {
#endif
...
... // C code here
...
#ifdef __cplusplus
}
#endif
```

### Always use <stdint.h> defined integer types

Include `<stdint.h>` in the top header file. The compiler will, with these types, always generate the expected variable sizes regardless of the used microcontrollers (MCU). Get in the habit of using the same `type` in code unless required by register requirements or using a negative integer. Consider integer range and size and decide which `type` to prefer. Consistent code helps maintenance, and errors are easier to find. For literals, long L, l, and unsigned long UL, ul is added after a constant (e.g. `0x0024UL`) and is 8 bytes in length.

| <stdint.h> types | range | size |
| -----------|-------|------|
| uint8_t | 0 to 255 | 1 byte |
| uint16_t | 0 to 65,535 | 2 byte |
| uint32_t | 0 to 4,294,967,295 | 4 byte |
| int8_t | -128 to 127 | 1 byte |
| int16_t | -32,768 to 32,767 | 2 byte |
| int32_t | -2,147,483,648 to 2,147,483,647 | 4 byte |

### Hardware register files

The header file for STM32F407 basic timers (TIM6 and TIM7) uses register addresses like this.

```c
f407hardwarebasictimers.h
```

Include this in the header file `basictimers.h`, i.e., which implements the custom functionality in the `basictimers.c` or the `basictimers.cpp`.

### Sample snippet from a hardware file

Typically all `#defines` is within the C++ check region. Here all relevant base register addresses, with offsets to targeted registers, are defined. The base address must always use the form `<register name>_<BASE_ADDR>`. Address derived with the offset can use a more descriptive form (all caps) since its use in user code—comments on every line.

```c
#ifdef __cplusplus
 extern "C" {
#endif

    #define TIM6_CR1_BASE_ADDR (0x40001000UL) // TIM6 base address
    #define TIM7_CR1_BASE_ADDR (0x40001400UL) // TIM7 base address

    // TIM6
    #define TIM6_CR1_ADDR (TIM6_CR1_BASE_ADDR + 0x0000UL) // offset 0x00
    #define TIM6_SR_ADDR (TIM6_CR1_BASE_ADDR + 0x0010UL) // offset 0x10
    #define TIM6_CNT_ADDR (TIM6_CR1_BASE_ADDR + 0x0024UL) // offset 0x24
    #define TIM6_PCS_ADDR (TIM6_CR1_BASE_ADDR + 0x0028UL) // offset 0x28
    #define TIM6_ARR_ADDR (TIM6_CR1_BASE_ADDR + 0x002CUL) // offset 0x2C

    //TIM7
    #define TIM7_CR1_ADDR (TIM7_CR1_BASE_ADDR + 0x0000UL) // offset 0x00
    #define TIM7_SR_ADDR (TIM7_CR1_BASE_ADDR + 0x0010UL) // offset 0x10
    ...

#ifdef __cplusplus
}
#endif
```

All `#defines` must be in all caps, with optionally '`_`' used to break long names.

### C or C++ in embedded programming

System development for embedded microcontrollers is mostly C-oriented. There are pros and cons considering the complexity and additional effort of learning C++ if you are unfamiliar with it, and in the end, this topic may very well be just a personal choice. 

### Recommended names used for C++ class and functions/methods

The file name (e.g., with `basictimers.h`) defines the class name. The class name and constructor is `CamelCased` with no lowercase first prefix. The in-class private methods or functions are all in `lowercaseCamelCase()` for best readability.

```cpp
class BasicTimers {
public:
    // Enable clocks for 'Timer 6 or Timer 7'
    BasicTimers(uint16_t basic_timer);

private:
    void enableTimer(uint16_t basic_timer);
    ...
};
```
### Structures, enums and typedef
Enums and struct are always in `CamelCase`, like `MyProgress` and `ComplicatedProcess`. So append a '`_t`' like shown for typedef of these aggregated constructs.

```cpp
typedef enum
{
    proc_started=0,
    proc_progressing=1,
    proc_completed=2
} MyProgress_t ;

typedef struct 
{
    float seed;
    uint16_t index;
    MyProgress_t state;
} ComplicatedProcess_t ;
```
Write the declaration of the variable `process_one` with the above typedef.
```cpp
ComplicatedProcess_t process_one;
```

### Variables

Use all lower case with an optionally '`_`' for readability in long names.

```cpp
uint16_t basic_timer = 0;   // OK
uint16_t basictimer = 0;    // OK also
uint16_t BasicTimer = 0;    // Wrong

```
Prepended private in-class variable begins with an '`_`' and all lowercase. Also, prepend `p` to pointer variables for clarity. Finally, *align* the '`*`' towards the `data type`, not the `variable name`.
```cpp
pivate:

    uint16_t  _counter;          // OK
    uint16_t  another_counter;   // Wrong

    uint32_t* p_index            // OK
    uint32_t* pindex             // OK
    uint32_t *pindex             // Wrong
    ...
```

One exception to the above lowercase rule is if part of the name is an established MCU-referenced register name, then use all caps for that part **only**. 

```c
int32_t* pRCC_APB1ENR_TIM = (uint32_t*) RCC_TIM_ENABLE_ADDR;  // OK
int32_t* p_RCC_APB1ENR_tim = (uint32_t*) RCC_TIM_ENABLE_ADDR; // OK (also)
int32_t *pRCC_APB1ENR_TIM = (uint32_t*) RCC_TIM_ENABLE_ADDR; // Wrong
int32_t *pRCC_APB1ENR_tim = (uint32_t*) RCC_TIM_ENABLE_ADDR; // Wrong
```
