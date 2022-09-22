# EVM puzzles

## My Solutions

- [x] Puzzle 1: We need to provide 8 as the callvalue since callvalue is loaded on the stack and JUMP then reads the top of the stack. This value is interpreted as the location in the stack to jump to. That location is required to be a JUMPDEST and in our case that lives at location "08". So we need to use a callvalue of 8.

- [x] Puzzle 2: Now, the callvalue is added to the stack first, then on top comes the CODESIZE. We see from the program given that the codesize is 10 (from 0 through slot/location 9). Then, a subtraction happens via SUB. Sub subtracts the top and top-1 elements like this: (top) - (top-1), so we get: CODESIZE-CALLVALUE (codesize is at the top since it was pushed second). So 10 - x needs to be fed into the following JUMP command so that we end up at the JUMPDEST at location 6. In other words, 10-x=6, or x=4. So if we send in a callvalue of 4 the JUMP will read 6 off the top of the stack and thus jump to 6 which is JUMPDEST and thus valid and after that, the program continues to a "STOP" command which halts execution and thus we don't hit the following REVERTS either.

- [ ] Puzzle 3:
- [ ] Puzzle 4:
- [ ] Puzzle 5:
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
