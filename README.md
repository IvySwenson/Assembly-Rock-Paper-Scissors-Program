# Assembly-Rock-Paper-Scissors-Program
# Rock-Paper-Scissors in Assembly Language

[Run the Project Online](https://lawlor.cs.uaf.edu/netrun/run)

## Overview
This project implements a simple Rock-Paper-Scissors game in x86-64 Assembly language. The program demonstrates essential low-level programming concepts such as:

- System calls for input/output
- Random number generation using `rdrand`
- Game logic with conditional jumps

Users play against the computer, and the game determines the outcome (win, lose, or draw) based on their choices.

## Features
- **Input Handling:** Accepts valid choices (0 = Rock, 1 = Scissors, 2 = Paper). Displays an error message for invalid input.
- **Random Computer Choice:** Uses the `rdrand` instruction to generate a random choice for the computer.
- **Game Logic:** Compares the player's choice with the computer's choice to determine the outcome.
- **Output Messages:** Displays results such as "You win!", "You lose!", or "It's a draw!".

## How to Run
1. Open the [NetRun Online Compiler](https://lawlor.cs.uaf.edu/netrun/run).
2. Paste the assembly code into the editor.
3. Press the **"Run It!"** button.
4. Input your choice when prompted:
   - `0` for Rock
   - `1` for Scissors
   - `2` for Paper
5. View the result in the output section.

## Input Instructions
- **Valid Inputs:** `0`, `1`, `2`.
- **Invalid Inputs:** Non-numeric or out-of-range values will trigger an error message: "Invalid input!".

## Output Examples
- **Draw:** "It's a draw!"
- **Win:** "You win!"
- **Lose:** "You lose!"
- **Invalid Input:** "Invalid input!"

## Code Walkthrough

### Input Validation
Ensures the user provides a valid choice:
```assembly
read_input:
    mov rax, 0                ; System call: read
    mov rdi, 0                ; File descriptor: standard input
    lea rsi, [player_choice]  ; Address to store input
    mov rdx, 1                ; Read 1 byte
    syscall                   ; Make system call

    mov al, [player_choice]   ; Load the input
    sub al, '0'               ; Convert ASCII to integer
    cmp al, 0                 ; Check if input is less than 0
    jl .invalid_input         ; Jump if invalid
    cmp al, 2                 ; Check if input is greater than 2
    jg .invalid_input         ; Jump if invalid
    mov [player_choice], al   ; Store the valid input
    ret

.invalid_input:
    mov rsi, msg_invalid_input
    mov rdx, 15               ; Length of "Invalid input!"
    call print_string         ; Print error message
    xor rax, rax              ; Return 0
    ret
```

### Random Number Generation
Generates a random choice for the computer:
```assembly
generate_random:
    push rbx              ; Save rbx register
    rdrand rax            ; Generate random number
    jc .valid             ; If carry flag is set, rdrand succeeded
    mov rax, 0            ; Default to 0 if rdrand failed
.valid:
    and al, 0x02          ; Limit range to 0–2
    pop rbx               ; Restore rbx register
    ret
```

### Game Logic
Determines the result of the game based on the player's and computer's choices:
```assembly
compare_choices:
    mov al, [player_choice]   ; Load player choice
    mov bl, [computer_choice] ; Load computer choice
    cmp al, bl                ; Compare choices
    je .draw                  ; If equal, it's a draw

    cmp al, 0                 ; If player chose Rock
    je .rock_vs
    cmp al, 1                 ; If player chose Scissors
    je .scissors_vs
    cmp al, 2                 ; If player chose Paper
    je .paper_vs

.rock_vs:
    cmp bl, 1                 ; Rock beats Scissors
    je .win
    jmp .lose

.scissors_vs:
    cmp bl, 2                 ; Scissors beats Paper
    je .win
    jmp .lose

.paper_vs:
    cmp bl, 0                 ; Paper beats Rock
    je .win
    jmp .lose

.draw:
    mov rsi, msg_result_draw
    mov rdx, 13               ; Length of "It's a draw!"
    call print_string         ; Print draw message
    ret

.win:
    mov rsi, msg_result_win
    mov rdx, 9                ; Length of "You win!"
    call print_string         ; Print win message
    ret

.lose:
    mov rsi, msg_result_lose
    mov rdx, 10               ; Length of "You lose!"
    call print_string         ; Print lose message
    ret
```

### Message Output
Prints the result using a system call:
```assembly
print_string:
    mov rax, 1            ; System call: write
    mov rdi, 1            ; File descriptor: standard output
    syscall               ; Invoke system call
    ret
```

## Challenges and Lessons Learned
1. **Stack Alignment:** Ensuring the stack was aligned to 16 bytes before system calls was crucial to avoid runtime errors.
2. **Register Preservation:** Properly saving and restoring non-volatile registers (e.g., `rbx`, `rbp`) ensured the program didn't corrupt the state of the main function.
3. **Error Handling:** Adding graceful handling for invalid inputs made the program more robust and user-friendly.

## Conclusion
This project demonstrates how assembly language can be used to create an interactive application. It combines low-level programming constructs with logical flow to provide a simple yet educational example of a classic game. By running this code in NetRun, users can see the power and limitations of assembly language in action.



