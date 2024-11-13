# m68k-computer
Documentation and code regarding my custom computer architecture based on the Motorolla 68000

## Components
* Motorolla 68000 Processor (MC68000)
* Many logic gates and inverters
* Many breadboards
* 2x AS6C4008 512kb RAM Chips
* ROM of some kind
* Debug LEDs

## Memory Map
```
ROM Space:    0x000000 - 0x07FFFF
RAM0 (512kb): 0x080000 - 0x0FFFFF
RAM1 (512kb): 0x100000 - 0x17FFFF
IO Space:     0x800000 - 0xFFFFFF
```

The memory map is designed, so that it's easy to use logic gates to enable/disable certain chips using their chip enable pins. if the 5th highest bit is set, we know it has to be in the first RAM chip, if the 4th highest bit is set, we know it has to be in the second RAM chip. If neither are on AND the IO Space bit (the highest bit) is not set, we know that we need to read from ROM.

IO Space is designed so that there's a completly different 8MB block just for IO devices to talk in. Hardware device accesses are performed through memory mapped IO. the 4 highest bits next to the IO Space Enable bit are used to form a 4-bit number, which are used to access up to 16 different devices connected on the bus. The data bus is shared for every single IO Device, the hardware peripherals themselves are responsible for figuring out which memory write belongs to them. and there's nothing preventing another device from reading CPU Writes that are meant for a different IO Device. However if a CPU Read occurs, and both devices respond, a conflict could occur.

## Video

It's very likely that a custom solution will be built using a microcontroller (Raspberry Pi Pico) which will be connected to the bus, aswell as provide VGA Output using its GPIO Pins. The Video chip will be crucial to making applications work performantely, as the Motorolla 68000 driving the framebuffer alone would be way too slow to be usable. The Video chip will provide the following features:
* 256 colors with VGA output
* 16 Hardware Sprites (all supporting transparency)
* Pattern Blit acceleration (for patterns up to 32x32 pixels)
* Fast rectangular region single color clear
* Mouse cursor (customaizable)
* Multiple drawing layers that all overlap, features include:
    * 4 Total hardware layers (1 of which being the background layer)  
    * Layers can be shifted vertically and horizontally individually (except the background layer)
