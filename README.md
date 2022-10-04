# EVM puzzles

## My Solutions

- [x] Puzzle 1: We need to provide 8 as the callvalue since callvalue is loaded on the stack (CALLVALUE is the amount of Wei we send in) and JUMP then reads the top of the stack. This value is interpreted as the location in the stack to jump to. That location is required to be a JUMPDEST and in our case that lives at location "08". So we need to use a callvalue of 8.

- [x] Puzzle 2: Now, the callvalue is added to the stack first, then on top comes the CODESIZE. We see from the program given that the codesize is 10 (from 0 through slot/location 9). Then, a subtraction happens via SUB. Sub subtracts the top and top-1 elements like this: (top) - (top-1), so we get: CODESIZE-CALLVALUE (codesize is at the top since it was pushed second). So 10 - x needs to be fed into the following JUMP command so that we end up at the JUMPDEST at location 6. In other words, 10-x=6, or x=4. So if we send in a callvalue of 4 the JUMP will read 6 off the top of the stack and thus jump to 6 which is JUMPDEST and thus valid and after that, the program continues to a "STOP" command which halts execution and thus we don't hit the following REVERTS either.

- [x] Puzzle 3: In this case, we are sending in CALLDATA. The first opcode computes its length (in bytes). So if we send in "0xff" that is 1 byte and CALLDATASIZE is 1. The jump instruction (second opcode) needs to jump to the JMPDEST (at location 4 in memory). So we need to pass in CALLDATA that has size 4 bytes, e.g.: 0xffffffff.

- [x] Puzzle 4: We are taking CODESIZE xor CALLVALUE. The codesize is fixed at 0xc. So we need a number where 0xc ^ (number) = 0xa, because 0xa is the JMPDEST. We can convert 0xc to binary in python with: bin(int("0xc", 16)) giving us: 0b1100. So "1100". We need 0xa=1010. So 1100^(something) = 1010. The xor is exclusive or, so only if it's a strict or condition does it give 1. Thus, going bit by bit we see that we need a pattern: (1100) ^ (0110) = (1010), so 0110=hex(0b0110)=0x6. So the value is 6.

- [x] Puzzle 5: We see that the callvalue is duplicated and then multiplied. In other words, the code squares the incoming callvalue. Then, it compares it to 0x0100 which is 256. If equal it will push 1 to the stack. Then, the JUMPDEST destination hex is pushed to the stack. Now, the JUMPI is reached. It takes 0x0c as the destination which we confirm is JUMPDEST, so all is good so far. But it only jumps *if* the following stack element is 1. And we want it to perform the jump to not revert. So we need 256 to equal our incoming value squared. In other words, we need to send in 16 (16^2=256). This makes JUMPI look like: `JUMPI 0x0c 1` which will do the jump to 0x0c.

- [x] Puzzle 6: In this case, we use CALLDATALOAD with an index of 0. In other words, we are reading the calldata with an offset of 0 (so we read the entire 32 bytes of data coming in). We need CALLDATALOAD(0) to return 0x0a because that will jump to 0x0a where the JUMPDEST is. So we need to represent 0xa with all its 32 bytes. This simply looks like this (we pad it): 0x000000000000000000000000000000000000000000000000000000000000000A (32 bytes) and use that to solve it.

- [x] Puzzle 7: The goal here in essence is to send in calldata that goes into CREATE to be deployed as a contract. We want that contract's code to be of size 1. CREATE in this case takes the arguments `0 0 CALLDATASIZE` (where left is top of stack). So this deploys all bytes on the calldata with 0 offset and also needs 0 wei (so we don't need to send in Wei as well). The goal therefore is to construct CALLDATA that, when called with CREATE says to CREATE "hey, I am a new contract that you just deployed, and my size is 1". This size is communicated via the `RETURN` opcode which we will need to use (deployed code that does not RETURN will fail). In other words, when code is deployed, eventually the deployed code will get to RETURN (and whatever the constructor's code is will be run before that of course). Here, since we don't actually care about any deployed code we don't need anything else than simply "deploying" a return statement that tells CREATE that the size of the deployed code is 1 (why 1? Because the following opcode compares the code size of the deployed code with 1 and only jumps if that is the size). Let's look at the RETURN opcode, it takes offset and size from the stack (in that order from the top). The offset we don't care about and can set to 0, but the size we'd like to be 1 of course. So let's start seeing what this would look like. First, to push 0 to the stack we need: 6000 (60 is PUSH1 -- pushes 1 byte -- and 00 is just zero as a 1 byte representation). So 0x6000 if deployed would just push 1 byte (00) onto the stack at the deployed contract. But it wouldn't return anything to CREATE (since we haven't called the return opcode of course) and hence this would fail. Okay, so we need some more code. In fact, we need to push the size first to the stack which we want to be 1: 0x6001 (push1 01). Okay, so if we do: 0x60016000 (push1 01, push1 00) this pushed 1 to the stack and then 0 to the stack at the deployed address. The stack therefore looks like [0, 1] (left is top, note how 0 is on top because it's called most recently). Now, if we call RETURN on that it will take in 2 elements from the stack and use 0 (the top of the stack) as offset and 1 as the size of the deployed code (whether it's actually 1 or whatever does not matter, RETURN will just tell CREATED that 1 byte was deployed). Exactly what we want, so add in the RETURN opcode: 0x60016000F3 (F3: the opcode for RETURN). This is the simplest deployed code we can have and RETURN will now tell CREATE that "hey, I am 1 byte in size". Great, that's the solution!

- [x] Puzzle 8: In this puzzle, the CALLDATA is basically a contract that we need to deploy (via CREATE). This contract when called with 0 wei and 0 arguments, need to revert (so that the returned value is 0). The returned value 0 is then compared to 0 and this allows the JUMP to jump to the JUMPDEST (over the reverts) and we can pass the puzzle. The question then is, what CALLDATA creates a contract (when called via CREATE) which simply reverts when no arguments or function selector is specified? This would be a contract with a fallback function. The fallback function just needs to contain a "revert();" statement as its sole content. So I created that contract in Remix and copied its bytecode and passed that in as the CALLDATA. 0x6080604052348015600f57600080fd5b50604b80601d6000396000f3fe6080604052348015600f57600080fd5b50600080fdfea2646970667358221220067
c37f9af88f892824f795eb5d0a412d7731528f8fb188222451fa7a601970f64736f6c63430008070033 is what I used (that represents a contract with a fallback function that simply reverts).

- [x] Puzzle 9: I need to send in calldata with size > 3. So I just send the smallet size I can which can still be multiplied by the call value to give 8 (you see later on after the initial calldata size check we multiply the calldatasize with the incoming callvalue and compare to 8). So I send in 0x00000001 that is 4 in size which is the smallest number >3 and can be muliplied by an integer to give 8. So the CALLVALUE is this integer, and I see that if I send in 2 I get: 4*2 = 8, that is what I need to complete. So I send as callvalue (first parameter to provide): 2, and I send in the calldata: 0x00000001.

- [x] Puzzle 10: For this one, I first see that I need calldatasize to equal 0 when mod'ed with 3. So I just pick 3 in size and thus send 0x000001. Next, for the callvalue, this needs to be less than the codesize which is 0x1b=27. Okay, I also need the callvalue + 0xa = 0x19, the JUMPDEST later on. Since 0xa = 10, I need the callvalue to be 0x19-0xa = 15 (or 0xf, but I need to provide the value in decimal, so 15 works). Since 15<27 that's great. That's the answer!

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

## Follow me on Twitter!

[![Twitter URL](https://img.shields.io/twitter/url/https/twitter.com/cryptojesperk.svg?style=social&label=Follow%20%40cryptojesperk)](https://twitter.com/cryptojesperk)

## License
This project uses the following license: [MIT](https://github.com/bisguzar/twitter-scraper/blob/master/LICENSE).
