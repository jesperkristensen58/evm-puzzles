# EVM puzzles

## My Solutions

- [x] Puzzle 1: We need to provide 8 as the callvalue since callvalue is loaded on the stack (CALLVALUE is the amount of Wei we send in) and JUMP then reads the top of the stack. This value is interpreted as the location in the stack to jump to. That location is required to be a JUMPDEST and in our case that lives at location "08". So we need to use a callvalue of 8.

- [x] Puzzle 2: Now, the callvalue is added to the stack first, then on top comes the CODESIZE. We see from the program given that the codesize is 10 (from 0 through slot/location 9). Then, a subtraction happens via SUB. Sub subtracts the top and top-1 elements like this: (top) - (top-1), so we get: CODESIZE-CALLVALUE (codesize is at the top since it was pushed second). So 10 - x needs to be fed into the following JUMP command so that we end up at the JUMPDEST at location 6. In other words, 10-x=6, or x=4. So if we send in a callvalue of 4 the JUMP will read 6 off the top of the stack and thus jump to 6 which is JUMPDEST and thus valid and after that, the program continues to a "STOP" command which halts execution and thus we don't hit the following REVERTS either.

- [x] Puzzle 3: In this case, we are sending in CALLDATA. The first opcode computes its length (in bytes). So if we send in "0xff" that is 1 byte and CALLDATASIZE is 1. The jump instruction (second opcode) needs to jump to the JMPDEST (at location 4 in memory). So we need to pass in CALLDATA that has size 4 bytes, e.g.: 0xffffffff.

- [x] Puzzle 4: We are taking CODESIZE xor CALLVALUE. The codesize is fixed at 0xc. So we need a number where 0xc ^ (number) = 0xa, because 0xa is the JMPDEST. We can convert 0xc to binary in python with: bin(int("0xc", 16)) giving us: 0b1100. So "1100". We need 0xa=1010. So 1100^(something) = 1010. The xor is exclusive or, so only if it's a strict or condition does it give 1. Thus, going bit by bit we see that we need a pattern: (1100) ^ (0110) = (1010), so 0110=hex(0b0110)=0x6. So the value is 6.

- [x] Puzzle 5: We see that the callvalue is duplicated and then multiplied. In other words, the code squares the incoming callvalue. Then, it compares it to 0x0100 which is 256. If equal it will push 1 to the stack. Then, the JUMPDEST destination hex is pushed to the stack. Now, the JUMPI is reached. It takes 0x0c as the destination which we confirm is JUMPDEST, so all is good so far. But it only jumps *if* the following stack element is 1. And we want it to perform the jump to not revert. So we need 256 to equal our incoming value squared. In other words, we need to send in 16 (16^2=256). This makes JUMPI look like: `JUMPI 0x0c 1` which will do the jump to 0x0c.

- [ ] Puzzle 6:
- [ ] Puzzle 7:
- [ ] Puzzle 8:
- [ ] Puzzle 9:

## Introduction

A collection of EVM puzzles. Each puzzle consists on sending a successful transaction to a contract. The bytecode of the contract is provided, and you need to fill the transaction data that won't revert the execution.

## How to play

Clone this repository and install its dependencies (`npm install` or `yarn`). Then run:

```
npx hardhat play
```

And the game will start.

In some puzzles you only need to provide the value that will be sent to the contract, in others the calldata, and in others both values.

You can use [`evm.codes`](https://www.evm.codes/)'s reference and playground to work through this.
