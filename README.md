## My embedded coding and naming style guide

### Table of contents

    - [ File naming style ]
    - [ Header include files ]
    - [ Always use <stdint.h> defined integer types ]
    - [ Hardware register files ]
    - [ Sample snippet from a hardware file ]
    - [ C or C++ in embedded programming ]
    - [ Names used for C++ class and functions/methods ]
    - [ Variable names ]

### File naming style

Files are named all in lower case, without any `_`,`-` or whitespace, i.e. all in one world. That ensures that used tools will not have any problem to read file, regardless of host operating system or build tools used.

```
syscalls.c          // OK
sys_calls.c         // Wrong
sys calls.c         // Wrong
SysCalls.c          // Wrong
```

Same as above, applies for directory names also i.e all lower case, to avoid path issues for any buid tool and/or operating system.

### Header include files

Always include guard defines to prevent multiple inclusions. Use `#ifndef` and name it based on the file name (`basictimers.h`). Use upper case letters only with `_` repacing dots, and end all with one `_`. No spaces and all in caps.

```c
#ifndef BASICTIMERS_H_
#define BASICTIMERS_H_
...
#endif /* BASICTIMERS_H_ */
```

Always include a C++ check and include the external header files outside this C++ check. Further, first include any external C standard library files header files, before any user header files.

```c
...
#include <stdint.h>
#include "f407hardwarebasictimers.h"

#ifdef __cplusplus
 extern "C" {
#endif
...
... // header content and user #define's
...
#ifdef __cplusplus
}
#endif
```

### Always use <stdint.h> defined integer types

Include `<stdint.h>` in the top header file. The compiler will with these types, always generate the expected variable sizes regardless of used micro controller (mcu). Get in the habit to use the same `type` in code, unless required by register requirements or use of a negative integer. Consider integer range, size and decide which `type` to prefer. Being consistent in code helps maintenance and any errors are easier to find. For literals, long L, l and unsigned long UL, ul are added after a constant (e.g. `0x0024UL`) and is of 8 bytes length.
| <stdint.h> types | range | size |
| -----------|-------|------|
| uint8_t | 0 to 255 | 1 byte |
| uint16_t | 0 to 65,535 | 2 byte |
| uint32_t | 0 to 4,294,967,295 | 4 byte |
| int8_t | -128 to 127 | 1 byte |
| int16_t | -32,768 to 32,767 | 2 byte |
| int32_t | -2,147,483,648 to 2,147,483,647 | 4 byte |

### Hardware register files

Keep all (and only) hardware defined register addresses in a separate header file named after the mcu. In this way it easier to port code to another mcu and easier to maintain. Name convention for this header file is `<mcu><"hardware"><name of the implementation header file>`. A header file for code to setup the basic timers (TIM6 and TIM7) for a STM32F407 with the used register addresses, could be named:

```c
f407hardwarebasictimers.h
```

Include this in the header file `basictimers.h`, i.e. which implements the custom functionallity in the `basictimers.c` or in the `basictimers.cpp`.

### Sample snippet from a hardware file

Typically all `#defines` is within the C++ check region. Here all relevant base register addresses, with offsets to targeted registers are defined. The base address must always use the form `<register name>_<BASE_ADDR>`. Address derived with the offset can use a more descriptive form (all caps) since its actually used in user code. Comments on every line.

```c
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
```

All `#defines` must be in all caps, with optionally `_` used to break long names.

### C or C++ in embedded programming

Traditionally, system development for embedded mcu's has been done in pure C. There are pro and cons considering the complexity and additional effort to learn C++, and in the end this topic may very well be just a personal choice. With C++, readability will be improved and because C++ may be more complex than C, it's basic language constructs are easy to use also for this usage. A search on internet will give more food for thoughts and then draw your own conclusion.

### Recommended names used for C++ class and functions/methods

If a class is used, it's name is given by the file name (e.g. with `basictimers.h`). The class name and constructor is camel cased, unless part of it is an estblished acronym, e.g. GPIO:

```c
class BasicTimers {
public:
    // Enable clocks for 'Timer 6 or Timer 7'
    BasicTimers(uint16_t basic_timer);

private:
    void enableTimer(uint16_t basic_timer);
    ...
};
```

The methods/functions are all in `lowercaseCamelCase()` for best readabilty.

### Variables

Use all lower case with an optionally `_` for readabilty in long names.

```c
uint16_t basic_timer = 0;   // OK
uint16_t basictimer = 0;    // OK also
uint16_t BasicTimer = 0;    // Wrong
```

One exception to the above rule is, if part of the name is an established mcu referenced registername, then use all caps for that part **only**. Also, prepend `p` to pointer variables for clarity. Align the `*` to type, not to the variable name.

```
int32_t* p_RCC_APB1ENR_tim = (uint32_t*) RCC_TIM_ENABLE_ADDR; // OK
int32_t* pRCC_APB1ENR = (uint32_t*) RCC_TIM_ENABLE_ADDR; // OK also
int32_t* pRCC_APB1ENR_TIM = (uint32_t*) RCC_TIM_ENABLE_ADDR; // Wrong
int32_t *pRCC_APB1ENR_tim = (uint32_t*) RCC_TIM_ENABLE_ADDR; // Wrong
```

