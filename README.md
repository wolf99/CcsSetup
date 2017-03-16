# TI Code Composer Studio Settings & Preferences
This helps me get the IDE back to the way I prefer it after a reinstall.

## Also install
 - ControlSUITE (and restart CCS)
 - MSP430Ware (use standalone installer then restart CCS)
 - UniFlash
 
Make sure to use the older CCS Resource Explorer. The newer one does not pick-up
the ~ControlSUITE~ newer version MSP430 materials.

## Plugins
 - Eclox (available on the Eclipse marketplace)

## Perspectives
#### CCS Edit
  - left-side:
    - Project Explorer
    - Target Configurations
    - Include Browser
  - right-side:
    - window-1:
      - Search (new)
      - Progress
    - window-2:
      - Problems
     - Breakpoints
     - Tasks
     - Memory Allocation
     - Advice
     - Console
  
#### CCS Debug
  - left-side:
    - Project Explorer
    - Target Configurations
    - Include Browser
  - right-side:
    - window-1:
      - Debug
      - Problems
      - Memory Allocation
      - Tasks
      - Advice
    - window-2
      - Breakpoints
      - Variables
      - Expressions
      - Registers
    - window-3
      - Disassembly
      - Memory Browser
       
## Preferences
  - General:
    - Appearance:
      - Theme: Dark
      - Colors and Fonts:
        - Basic:
          - Match highlight background color: RGB(80, 97, 103)
        - Git:
          - Diff Removed (Foreground) : RGB(218, 218, 218)
          - Ignored Resource (Foreground): RGB(128, 128, 128)
          - Uncommited Change (Foreground): RGB(255, 128, 128)
    - Editors:
      - Text Editors:
        - Insert spaces for tabs: true
          - show print margin: true
        - Appearance color options:
          - Current line highlight: RGB(56, 56, 56)
          - Print margin: RGB(63, 65, 68)
          - Selection foreground color: RGB(168, 179, 179)
          - Selection background color: RGB(0, 0, 0)
          - Background color: RGB(32, 32, 32)
        - Accessibility:
          - Use saturated colors in overview ruler: true
        - Annotations:
          - C/C++ Occurances: RGB(64, 0, 64)
          - C/C++ Write Occurances: RGB(128, 0, 255)
          - Search Results: RGB(83, 58, 88)
        - Spelling:
          - Platform Dictionary: English (United Kingdom)
          - Encoding: Default (UTF-8)
    - Workspace:
      - Text file encoding: Other: UTF-8
  - C/C++: (Show advanced settings)
    - Build:
      - Console:
        - Output text color: white
    - Code Style:
      - Formatter:
        - Custom Profile: (based on CCS)
          - Indentation:
            - Tab policy: Spaces only
            - Statements within 'switch' body: true
          - Control Statements:
            - Insert new line before 'else' in an 'if' statement: true
            - Keep simple 'if' on one line: true
    - Editor:
      - Appearance color options:
        - Inactive code highlight: RGB(41, 41, 41)
        - Documentation tool comments:
          - workspace default: Doxygen

## Adjustments to Support Packages

### Digital Power Library
  - The function `DLOG_BuffInit()` in the file DPlib.h uses poor parameter types,
  the function should be commented out if not used, or native types used.
 
### IQ Math Library
  - For TEST purposes, if `IQmathLib_helper.h` is used then the` FLOAT_MATH` 
  version of `_IQsat()` (ln #3759) of IQmathLib.h should be commented out to 
  prevent duplicate definitions.

### F2802x Driver Library
  - The device support driverlib.lin requires rebuilding with the correct flash 
wait states set in the file `flash.c` as follows:
    - Paged wait state  = 2
    - Random wait state = 2
    - OTP wait state    = 2
  
  See [this e2e thread][1] for how to rebuild the library after changes are made.
  

  - The device driver header files do not correctly include the types used for 
  their function parameters. Include `bspfix.h` **_before_** any device driver 
  header includes to remedy this; it defines `uint8_t` and `bool_t`. 
 
  See [this e2e thread][2] for more information.
 
 
### Ramfuncs Locations
  - The linker command file should define the ramfuncs locations in SECTIONS as 
  follows:
  
  ```C
     ramfuncs            : LOAD = FLASHA,
                         RUN = PRAML0,
                         LOAD_START(_RamfuncsLoadStart),
                         LOAD_END(_RamfuncsLoadEnd),
                         RUN_START(_RamfuncsRunStart),
                         PAGE = 0
  ```
  
  - To cater for newer compiler versions the following should also be present
  
  ```C
  #ifdef __TI_COMPILER_VERSION
     #if __TI_COMPILER_VERSION >= 15009000
      .TI.ramfunc : {} LOAD = FLASHA,
                         RUN = PRAML0,
                         LOAD_START(_RamfuncsLoadStart),
                         LOAD_END(_RamfuncsLoadEnd),
                         RUN_START(_RamfuncsRunStart),
                         PAGE = 0
     #endif
  #endif                           
  ```
  
  - It will also be necessary to add extern declarations for these variables to 
  the source file they are to be used in (usually `main.c`):
  
  ```C
  /* External symbols created by the linker command file. DSP28 examples will use
   * these to relocate code from one LOAD location in Flash to a different RUN
   * location in internal RAM. See f2802x_globalprototypes.h
   */
  extern uint16_t RamfuncsLoadStart;
  extern uint16_t RamfuncsLoadSize;
  extern uint16_t RamfuncsRunStart;
  extern uint16_t RamfuncsLoadEnd;
  ```

[1]: https://e2e.ti.com/support/microcontrollers/c2000/f/171/t/557285 "e2e thread"
[2]: https://e2e.ti.com/support/microcontrollers/c2000/f/171/t/557285 "e2e thread"

