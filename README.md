CE5
======
#Task1

```mips
	addi $S0, $0, 44 #Adds 44 to S0
	addi $S1, $0, -37 #Adds -37 to S1
	add  $S2, $S0, $s1 #Adds S0 and S1 in S2
	sw   $S2, 84($0) #Stores 84/x54 in S2
```

#Task2
```mips
	addi $S0, $0, 44 #1st Instruction
	addi $S1, $0, -37 #2nd
	add  $S2, $S0, $s1 #3rd
	sw   $S2, 84($0) #4th
```
##Machine Codes(Binary)
1st- 001000 00000 100000 0000000000101100

2nd- 001000 00000 10001 1111111111111011

3rd- 000000 100000 10001 10010 00000 100000

4th- 101011 00000 10010 0000000000110110

##Macine Code (Hex)
1st- 0x20100026

2nd- 0x2011FFDB

3rd- 0x02119020

4th- 0xAC120054
