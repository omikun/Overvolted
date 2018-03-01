# Overvolted
A couch multiplayer game where players work together to operate a CPU at a microarchitecture level

## steps for building Overclocked (or Overvolted, since overclocked is taken)

Collidable objects:

    objects that block player/characters from moving
    objects that impede or gets pushed off by chars (dropped stuff)
    
Pickup objects:

    Player select nearby objects for pickup
        overcooked version: a selectable tile near playerforward (don't count floor)
    Cheaper crappier way: 
        anything that collides with selector sticked to player.forward
        
## Story/gameplay:

    Player operates ATM, Allmighty Thinking Machine to appease the mob. If player takes too long, mob will progressively damage ATM, throwing other obstacles at player.
    
    Obstacles include:
        power failure - takes longer to tick blocks
        block failure - must go and fix block before continuing
        bad data - must reload or recompute
        clock failures - reconnect clock lines
        
    Blocks:
        every block must be clocked in order to really work (produce result, latch data, etc)
        instruction stream
            player can fetch (copy out) instructions from PC slot
            player can shift PC up or down
            PC auto shift to next inst after fetch
        decoder
            player can insert inst in, then clock it, and it'll show assembly
            player can take an immediate operand out
        copiernormal division and mod
            a convenience way to backup imm or other outputs
        adder
            takes 2 data and adds them
        div/mod
            normal division and mod
        register file
            each register has input/output terminals, load data in, clock, then data is stored
        clock
            just ticks other connected units
        data lines?? no need to manually move data around!

    Player can circumvent program by manually producing desired result
        instead of accumulating 1 42 times, it can do add to itself to get to 32, then add others
        maybe not even use RF?

## Gameplay

    instructions are loaded to buffer, 
    player must fetch instructions to decoder, 
    a micro-op display will light up 
        (ex. mov imm r1, add r2, r1, 4, st r2, disp)
    player move data packets from imm/RF to ALU, from ALU to RF, or from RF to memory
        memory can be display or main memory

### Example missions:
    Example 1:
        The peasants want a demonstration of the power of the Almighty Thinking Machine (ATM)!
        Let's blow their minds!
        mov 40 r1
        add r1 r1 1
        add r1 r1 1
        st MAIN r1
        END
    Example 2:
        It seems the peasants are too stupid to read our memory. Let's display it to the screen:
        st DISP r1
        END
    Example 3:
        The peasants can't even read numbers greater than 10. We will have to break it into two numbers:
        mod r2 r1 10
        div r1 r1 10
        st disp r1
        st disp r2
        END
    Example 4:
        The peasants don't understand how impressive that was, so we wil give something that will overwhelm their puny noodles.
        mov r0 0
        mov r1 24
        mov r2 7
        mov r3 12
        add r0 r1
        add r2 -1
        bnz r2 -3
        mov r1 r0
        mov r0 0
        add r0 r1
        add r3 -1
        bnz r3 -3
        st DISP r0
        END
    Example 5:
        Ok, the peasants are not content with our powers and they are about to riot. We must convert our previous number into digits less than 10.
        st STACK -1
        mod r1, r0, 10
        st STACK, r1
        div r0, r0, 10
        bnz r0 -4
        ld r0, STACK
        blz r0, 2
        st DISP r0
        jmp -4
        END

## ISA:
        mov R1, R2/imm  move r2 or imm to r1
        add R1, R2/imm  add r2 or imm to r1 and save to r1
        st loc, R1      store r1 to location DISP or MAIN
        blz r1, offset  branch if r1 is less than 0, next instruction is offset to current next
        bgt r1, offset  branch if r1 is greather than 0
        bez r1, offset  branch if r1 is equal to 0
        bne r1, offset  branch if r1 is not 0
        jmp offset      unconditional branch to offset
        div r1, r2, r3
        mod r1, r2, r3
        SHD             shuts down ATM
### Logic:
    Blocks - decoder, alu, register file, etc
    Blocks can be ticked by:
        a connected clock
        manually
    A clock connects to one or more blocks and can be ticked by:
        quartz 
            - auto ticking at set intervals 
            - or randomly! say a broken quartz...?
        manuall
    detect data hazard - when a register is used before it is written
        implementation - each instruction expects to use a certain version of a register
            - version number is the instruction # thatwrote to that register,
 
