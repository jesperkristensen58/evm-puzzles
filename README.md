# EVM puzzles

## My Solutions

- [x] Puzzle 1: We need to provide 8 as the callvalue since callvalue is loaded on the stack and JUMP then reads the top of the stack. This value is interpreted as the location in the stack to jump to. That location is required to be a JUMPDEST and in our case that lives at location "08". So we need to use a callvalue of 8.

- [ ] Puzzle 2:
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
