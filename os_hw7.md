# os homework 7
于新雨 计25 2022010841
## 第一题
页表放到了第 0x11 页面上 
```   
virt addr: 0x6c74
pde index:27, pde char: 0xa0, valid: 128, pfn: 0x20
pte index:3, pte char: 0xe1, valid: 128, pfn: 0x61
page offset: 20, value: 0x6, phys addr: 0xc34

virt addr: 0x6b22
pde index:26, pde char: 0xd2, valid: 128, pfn: 0x52
pte index:25, pte char: 0xc7, valid: 128, pfn: 0x47
page offset: 2, value: 0x1a, phys addr: 0x8e2

virt addr: 0x3df
pde index:0, pde char: 0xda, valid: 128, pfn: 0x5a
pte index:30, pte char: 0x85, valid: 128, pfn: 0x5
page offset: 31, value: 0xf, phys addr: 0xbf

virt addr: 0x69dc
pde index:26, pde char: 0xd2, valid: 128, pfn: 0x52
pte index:14, pte char: 0x7f, valid: 0, pfn: 0x7f
Fault (page table entry not valid)

virt addr: 0x317a
pde index:12, pde char: 0x98, valid: 128, pfn: 0x18
pte index:11, pte char: 0xb5, valid: 128, pfn: 0x35
page offset: 26, value: 0x1e, phys addr: 0x6ba

virt addr: 0x4546
pde index:17, pde char: 0xa1, valid: 128, pfn: 0x21
pte index:10, pte char: 0x7f, valid: 0, pfn: 0x7f
Fault (page table entry not valid)

virt addr: 0x2c03
pde index:11, pde char: 0xc4, valid: 128, pfn: 0x44
pte index:0, pte char: 0xd7, valid: 128, pfn: 0x57
page offset: 3, value: 0x16, phys addr: 0xae3

virt addr: 0x7fd7
pde index:31, pde char: 0x92, valid: 128, pfn: 0x12
pte index:30, pte char: 0x7f, valid: 0, pfn: 0x7f
Fault (page table entry not valid)

virt addr: 0x390e
pde index:14, pde char: 0x7f, valid: 0, pfn: 0x7f
Fault (page directory entry not valid)

virt addr: 0x748b
pde index:29, pde char: 0x80, valid: 128, pfn: 0x0
pte index:4, pte char: 0x7f, valid: 0, pfn: 0x7f
Fault (page table entry not valid)
```

写了脚本：   
```py
import re
phys_content = '''
page 00: 7f 7f 7f 7f 7f 7f b2 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 01: 01 19 1d 11 05 1a 1e 15 0f 0b 0a 06 14 0d 06 03 14 09 0f 0a 1b 1b 00 06 0a 1e 18 0a 06 14 19 1c 
page 02: ba 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 9f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f a4 7f 7f 7f 7f 
page 03: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 04: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 9c 
page 05: 1e 12 0c 05 0f 1e 17 10 1a 07 0f 1d 11 0e 08 10 1d 00 18 19 1b 16 19 10 11 0d 01 1a 11 06 0f 0f 
page 06: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 07: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 08: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f bd 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 09: 06 17 04 06 05 0b 01 0b 12 15 1a 02 13 06 0a 11 19 14 1e 00 09 0e 01 01 0b 11 04 02 09 16 11 1e 
page 0a: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 0b: 0f 1c 07 09 12 11 11 0c 01 12 08 13 0d 08 1e 09 1e 0e 10 08 05 15 0e 12 05 0f 14 17 14 0c 15 12 
page 0c: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 0d: 09 08 01 00 04 0c 1c 13 07 15 08 0f 09 1d 0a 18 13 1a 12 1a 0c 15 13 10 11 10 0c 1b 13 11 01 0f 
page 0e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 0f: 7f 7f 7f f1 7f 7f 7f b4 7f 7f ca c2 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 10: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 11: da f7 f2 a8 96 c5 9d 94 c8 b9 7f c4 98 e5 7f 7f d3 a1 82 8f a6 fb bf f0 7f 84 d2 a0 88 80 c9 92 
page 12: 7f 7f dd 7f 7f 7f 7f 7f 7f 7f 7f 95 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 13: 1c 00 04 14 01 0b 04 0e 1a 1c 01 01 1a 01 08 03 02 00 13 17 15 1a 14 0c 13 1e 13 07 01 1c 12 0a 
page 14: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f ad 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 15: 15 11 1c 05 0c 16 01 03 10 08 03 08 13 10 02 02 1a 13 05 1a 00 0b 1a 16 08 1b 12 1a 1b 0d 14 10 
page 16: 7f 7f 7f 7f 7f d9 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 17: 15 09 1c 0d 0e 00 00 03 13 14 1d 0d 15 0a 02 0d 15 18 1d 19 11 11 0f 0e 15 11 1a 0d 0e 19 14 10 
page 18: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f b5 7f 7f fa 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 93 7f 7f 7f 7f 7f 7f 
page 19: 0d 1b 11 11 0c 06 13 02 18 01 17 0b 15 12 04 10 02 07 0b 08 14 1e 0b 19 06 15 0a 10 02 19 06 0e 
page 1a: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 1b: 17 01 15 16 0a 0f 05 16 01 1e 19 13 08 1c 1d 04 18 1a 14 15 0d 1c 1e 0b 18 0d 05 0a 03 1c 1d 03 
page 1c: 07 06 0a 0e 1b 14 13 1a 0b 0d 07 19 1b 1c 12 03 02 18 1b 10 1c 1c 17 0b 0e 0a 0c 0e 00 03 05 11 
page 1d: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f a5 7f 7f 7f 7f 7f 7f 7f 
page 1e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 1f: 07 02 11 02 02 13 09 18 0f 1a 04 0f 18 02 1d 05 18 1e 19 09 03 0f 1c 09 1b 04 1c 00 09 1b 18 1c 
page 20: 7f 7f 7f e1 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f db 7f 7f 7f 
page 21: 7f 7f 7f 7f 7f 7f 99 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f d5 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 22: 11 1c 0e 08 18 19 00 14 02 03 1b 01 07 1e 0d 03 05 07 17 03 1c 0b 1e 1c 09 07 0e 03 14 01 00 1e 
page 23: 0c 12 1e 15 09 10 0d 0f 0c 05 0c 0c 06 19 0b 04 11 1a 18 13 16 0a 04 07 0a 08 0e 04 04 07 06 18 
page 24: 1e 07 0f 16 15 0d 18 0f 16 0f 1e 16 02 04 1d 07 00 07 0e 1d 0c 16 19 02 12 1e 11 10 0a 1d 1e 03 
page 25: 06 1b 07 11 0e 0a 0c 00 05 13 17 06 05 1c 03 18 1b 04 19 04 01 08 0a 12 0d 18 14 03 06 17 03 1d 
page 26: 7f 7f 7f 7f 7f a7 7f 7f 7f 7f 7f 7f 7f f5 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 97 7f 7f 
page 27: 0f 12 11 1e 05 13 1e 16 11 0b 0c 1d 1b 14 1b 1c 1a 0b 0e 18 0b 17 0e 0a 0e 03 0a 0c 00 05 08 1a 
page 28: 7f 7f 7f e8 7f 7f aa 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 8b 7f 7f 7f 7f 7f 7f 7f 7f 7f f9 7f 7f 7f 7f 
page 29: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 2a: 1e 14 18 0e 0e 0f 17 16 06 0d 10 11 1c 1a 04 0b 03 00 02 05 17 14 18 08 04 1e 19 1d 00 0c 13 16 
page 2b: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 2c: 13 12 0e 00 07 05 12 1a 18 18 19 07 1a 14 02 00 00 17 07 03 13 0a 02 04 10 05 08 16 0e 09 0e 00 
page 2d: 18 1c 09 1b 0a 14 1e 17 01 0d 0b 09 19 0d 15 13 10 01 14 1b 05 13 0f 0a 16 1e 00 1b 0b 19 05 16 
page 2e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 2f: 0a 17 0c 0b 11 06 02 07 00 14 0e 13 11 01 19 19 00 0d 18 0c 1a 15 14 1c 18 12 01 0e 15 10 12 0a 
page 30: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 31: 0d 0c 1a 12 16 1b 17 0f 17 13 14 13 0c 13 13 1d 18 1a 17 19 12 0a 08 15 1b 10 04 19 0f 0e 01 0f 
page 32: 18 1e 1c 14 0a 07 18 1c 1d 05 12 0f 0d 18 1d 16 15 15 14 10 07 18 03 13 0b 11 13 0e 1e 07 00 1d 
page 33: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 34: 09 07 17 00 05 0d 00 13 12 19 06 08 10 08 12 07 15 18 19 1e 10 0f 1a 17 11 0b 08 03 19 03 17 10 
page 35: 03 1b 0f 06 12 19 00 00 04 16 04 15 15 10 06 1e 18 10 06 14 0c 11 09 13 01 09 1e 1b 09 1a 09 1d 
page 36: 0b 14 00 0f 1d 15 0c 15 12 06 06 1b 08 02 19 10 0b 0f 16 05 14 16 19 08 12 07 11 05 18 1a 0a 06 
page 37: 07 08 10 0e 0c 03 0b 14 10 10 1a 16 15 00 09 15 04 1c 04 1b 06 1a 1a 0a 1b 04 1a 0b 0d 03 12 08 
page 38: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 39: 7f 7f 7f 7f 7f 7f 7f 7f 7f cb 7f 7f 7f 7f cc 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f a3 
page 3a: 1b 07 0d 15 1c 15 13 0b 0d 13 0b 18 00 17 17 09 00 0a 12 18 1a 06 02 03 1e 14 03 15 1d 12 07 1d 
page 3b: 0e 0f 05 10 0d 1b 16 0e 04 04 1e 12 06 19 06 0e 1b 03 03 01 04 0b 09 08 00 0f 0d 16 09 12 09 17 
page 3c: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 3d: 14 1a 00 05 0a 09 19 07 09 0f 1d 09 13 01 0d 1c 06 0b 14 11 11 12 14 0a 0a 0c 10 11 1b 0c 0d 19 
page 3e: 00 07 1b 01 14 0f 1e 1e 03 08 1e 0a 05 1c 13 09 11 0d 0e 11 05 13 1d 12 18 08 04 00 1e 03 0b 14 
page 3f: 7f 7f e7 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f e2 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 40: 0d 15 16 07 0f 0e 06 0c 11 1c 1c 08 14 01 02 0f 1b 04 17 1b 09 15 1a 0b 15 16 12 1a 1b 1d 11 05 
page 41: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 42: 01 0a 0c 06 1b 0d 14 16 17 16 17 07 1e 04 1c 1a 1a 01 02 19 0e 0b 1e 01 10 0d 03 0c 15 1b 00 10 
page 43: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 44: d7 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f ac 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 45: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f be e9 b7 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 46: 11 13 02 05 1d 08 02 08 16 08 06 08 0e 16 09 1b 1e 19 02 09 1c 1a 04 0d 0b 17 00 09 17 1b 01 12 
page 47: 05 1e 1a 03 0a 16 16 1d 0d 19 14 09 12 1b 1a 0f 12 01 07 18 0c 05 11 15 14 0b 0d 0f 18 10 0c 0f 
page 48: 9b 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 49: 7f 7f 7f 7f 7f 7f 7f dc 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f ec 7f fc 7f 7f 7f 
page 4a: 09 10 08 00 09 02 0f 0f 1a 17 17 1e 08 0b 07 03 0f 0f 04 1c 1e 02 00 01 16 1a 02 00 10 0a 00 00 
page 4b: 13 17 07 01 19 09 19 1d 13 03 1a 13 07 06 0f 03 1c 15 19 0b 1c 04 16 07 00 03 06 17 0b 0e 13 08 
page 4c: 09 01 1c 1e 1e 03 06 13 1e 10 15 14 08 10 09 07 02 08 1e 0d 14 13 1d 0c 09 0a 09 1a 1b 09 0a 10 
page 4d: 00 1d 1c 13 0b 11 1b 0e 18 12 0d 1c 0c 12 01 0e 01 15 00 01 03 04 0f 0b 08 1e 1c 14 18 19 07 19 
page 4e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 4f: 0d 19 0d 19 01 16 03 0c 0d 05 0d 1a 01 06 1e 0d 0c 1c 18 05 12 05 18 11 18 02 1c 07 1a 0d 1b 03 
page 50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 51: 0d 04 00 04 08 0e 00 02 18 1e 0d 0e 12 03 10 14 1d 13 10 0c 1c 10 0e 16 0d 02 12 1a 0b 02 03 1c 
page 52: 7f 7f 7f 7f 7f 7f 7f c6 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f c7 7f 7f df d1 7f 7f 
page 53: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f af 7f 7f 7f 7f 7f c0 7f 7f 
page 54: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 55: 08 0e 18 1a 18 14 02 0c 14 09 0f 1c 03 1a 03 0b 1c 06 10 0c 1b 1e 08 0f 1b 10 06 17 0a 0f 00 1e 
page 56: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 57: 0f 1e 07 16 02 05 1c 06 17 12 1a 0b 0a 09 1a 12 1d 1b 04 11 03 01 02 1a 18 19 0a 13 18 0b 11 06 
page 58: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 59: 07 0f 0f 1c 02 10 11 04 0b 04 1b 0a 02 0e 10 1b 16 06 1c 00 15 01 19 05 18 19 17 03 0c 03 16 1e 
page 5a: 7f 7f 7f b1 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 85 7f 
page 5b: 04 0e 13 04 02 12 07 13 05 1a 09 0d 11 1b 1c 1a 15 02 05 10 1e 16 05 0b 1d 0f 1a 1a 18 13 14 0a 
page 5c: 19 1e 07 06 17 17 0f 0f 0c 1b 18 12 01 1a 0e 05 09 15 00 03 09 1b 17 1e 10 11 11 10 10 19 1d 0c 
page 5d: 07 16 01 0f 11 15 1c 18 11 0f 00 11 11 17 05 12 01 16 19 0d 15 14 09 02 17 0b 05 0d 19 1d 11 1e 
page 5e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 5f: 15 05 0d 04 16 14 07 01 1d 19 11 10 1a 0e 0c 0a 07 00 14 0c 11 01 0b 04 03 08 19 0c 0c 12 07 00 
page 60: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 61: 0f 0d 14 18 02 00 19 0d 17 00 0d 16 07 1d 1b 00 00 10 1d 0b 06 0d 00 06 0d 0f 07 07 06 0e 08 00 
page 62: 0f 11 0b 09 0d 10 0e 1a 02 06 1d 12 13 13 07 0c 06 04 1e 17 1b 00 15 09 0f 14 00 0b 11 1b 0f 09 
page 63: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 64: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 65: 7f 7f 7f 7f 7f 7f 7f 7f 7f ff 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 89 7f 7f 7f 7f 
page 66: 02 01 0b 16 09 04 18 19 1a 09 0d 07 11 0a 0a 18 1d 12 03 14 0d 1e 16 19 15 10 1b 19 09 04 0b 10 
page 67: 0a 03 04 07 02 10 15 11 15 07 06 11 1b 0d 00 00 09 13 02 06 15 1e 0a 12 10 00 0a 04 07 17 15 01 
page 68: 09 0a 0a 17 0f 10 04 1c 0a 0a 02 1e 0e 1c 1c 1e 19 1c 1c 18 04 10 11 1e 18 15 17 0b 1d 13 0c 0e 
page 69: 1d 11 13 16 09 12 0e 0f 0c 10 06 07 06 12 07 18 1c 17 0a 1d 0a 0b 12 14 0c 18 1a 08 06 0c 15 14 
page 6a: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 6b: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 6c: 00 1b 13 09 0d 17 18 05 13 05 1e 0d 1c 16 12 08 10 04 04 16 0b 17 07 16 16 09 03 0c 0f 03 05 01 
page 6d: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 6e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 6f: 0d 1e 06 03 04 12 03 08 06 01 1d 0a 1d 16 06 1c 00 1e 01 07 11 00 17 02 19 01 10 06 08 0f 0b 0c 
page 70: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f cf 7f 7f 7f 7f 7f 7f 7f 7f 81 b6 7f 7f 7f 7f 7f 
page 71: 06 01 15 19 1d 13 0a 19 03 15 02 0c 0f 0b 05 07 19 0e 11 06 16 0a 12 1c 1e 01 18 1a 09 0b 11 1c 
page 72: 7f 7f f3 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 8d 7f 7f 7f a2 7f 7f 7f 7f 7f 7f 7f 7f e6 7f 7f 7f 
page 73: 0e 19 05 04 1c 08 01 0f 1e 19 0c 1e 18 1a 14 0c 1c 0e 1c 11 1c 0e 0d 12 09 04 12 1a 08 1a 18 18 
page 74: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 75: 0f 1a 1e 17 0d 08 03 19 04 11 0e 01 06 19 10 1c 1c 02 15 01 0d 0d 1d 12 05 0f 10 06 0b 1b 1e 18 
page 76: 08 10 0d 17 1a 07 08 15 0c 04 06 11 12 1d 10 12 04 0c 08 15 08 06 0b 0e 0a 12 05 1b 15 10 01 0a 
page 77: 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f bb 7f 7f 7f 7f 7f 7f 7f 7f 
page 78: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 79: 01 08 13 1d 06 07 1a 0a 0c 0b 01 1d 15 00 03 04 0b 1b 04 07 09 0f 1b 10 1c 10 0d 1b 12 0e 0f 0b 
page 7a: 12 18 1a 06 02 12 0b 16 09 0d 19 02 0c 04 10 16 1e 17 04 0d 10 13 15 1e 1d 06 04 1e 04 1e 03 12 
page 7b: 7f f6 7f 7f 7f 7f 7f ef 7f 7f cd 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 
page 7c: 09 0f 0b 10 00 0d 0d 09 0c 18 15 0f 14 0b 06 00 08 12 1b 19 0f 1e 0e 19 0c 17 1e 09 05 13 10 0b 
page 7d: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 7e: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
page 7f: 11 03 11 00 1b 0a 0b 11 13 12 0f 13 1a 0d 0f 19 00 04 0a 06 1b 04 03 09 0f 19 1e 1a 12 01 01 13 
'''

root_page_table_idx = 0x11

def parse_phys_content(content_string):
    pages = []

    lines = content_string.strip().split('\n')
    
    for line in lines:
        if not line.strip():
            continue
        
        if line.startswith('page '):
            values_part = line.split(':', 1)[1].strip()

            hex_values = values_part.split()
            
            # Convert each hex value to integer and add to the page
            page_values = []
            for hex_val in hex_values:
                try:
                    val = int(hex_val, 16)
                    page_values.append(val)
                except ValueError:
                    page_values.append(hex_val)
            pages.append(page_values)
    
    return pages


pages = parse_phys_content(phys_content)

def get_info(virt_addr):
    pde_idx = virt_addr //(0x20*0x20)
    pde_char = pages[root_page_table_idx][pde_idx]
    valid = pde_char & 0x80
    pfn = pde_char & 0x7F
    print(f"pde index:{pde_idx}, pde char: {hex(pde_char)}, valid: {valid}, pfn: {hex(pfn)}")
    if valid == 0:
        print("Fault (page directory entry not valid)")
        return
    pte_idx = (virt_addr // 0x20) % 0x20
    pte_char = pages[pfn][pte_idx]
    valid = pte_char & 0x80
    pfn= pte_char & 0x7F
    print(f"pte index:{pte_idx}, pte char: {hex(pte_char)}, valid: {valid}, pfn: {hex(pfn)}")
    if valid == 0:
        print("Fault (page table entry not valid)")
        return
    page_offset = virt_addr % 0x20
    value = pages[pfn][page_offset]
    phys_addr = (pfn << 5) | page_offset
    print(f"page offset: {page_offset}, value: {hex(value)}, phys addr: {hex(phys_addr)}")

virt_addr_list = [0x6c74, 0x6b22, 0x03df, 0x69dc, 0x317a, 0x4546, 0x2c03, 0x7fd7, 0x390e, 0x748b]

for i in virt_addr_list:
    print(f"virt addr: {hex(i)}")
    get_info(i)
    print()
```

## 第二题
```rs
    pub fn print_translation_dbg(&self, va: VirtAddr) {
        let pte = self.find_pte(va.clone().floor()).unwrap();
        println!("va = {:?}, pte = {:?}", va, pte);
        let aligned_pa: PhysAddr = pte.ppn().into();
        let valid = pte.is_valid();
        if !valid{
            println!("page fault: pte is invalid");
            return;
        }else{
            offset = va.page_offset();
            let aligned_pa_usize: usize = aligned_pa.into();
            let pa = aligned_pa_usize + offset;
            println!("va = {:?}, pa = {:?}", va, pa);
            // get content
            let content = unsafe { *(pa as *const u8) };
            println!("content = {:?}", content);
        }
    }
```
在 os/src/mm/page_table.rs 的 `impl PageTable` 里面实现的