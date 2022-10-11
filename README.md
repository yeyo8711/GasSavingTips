# Gas Saving Tips



***How to save gas in Solidity***

1. **Function Names**
- Function Selector = The first 4 bytes of the hashed keccak256 function *name*
- The compiler will sort them in hexidecimal order
- Solidity will check to see which function was included in transaction.data until it reaches the right one
- If you have a gas sensitive function, try to have its function selector at the top
- Example: 

`function setArray() external {}//gas: 144`

`function aSetArray() external {} //gas: 122`

2. ***Less than VS. Less than or equal to***
- There is no bytecode to compare if one integer is greater or equal to (or <=)
- Solidity will use an extra opcode "ISZERO" to invert the equation hence more gas
- Always use > or < instead of >= and <=
- Strict inequalities only require one opcode, LT or GT
- Example: 

`function compare() external pure returns(bool) {
        return 3 > 2;
        // 327 gas
    }`
    
`function compare() external pure returns(bool) {
        return 3 >= 2;
        // 330 gas
    }`

3. ***Bit Shifting***
- Shifting bits using the Bitwise Operators >> and << can help reduce gas but is very dangerous due to overflow
- Opcode for multiplication (MUL) cost 5 gas, whereas the opcode for shifting(SHL or SHR) cost 3 gas
- Example: 

    `function shiftLeft() external pure returns(uint256){
        return 10 << 1;
        // gas used 21445
    }`
    
    `function multiply() external pure returns(uint256){
        return 10 * 2;
        // gas used 21379
    }`

4. ***Reverting Early***
- When a transaction reverts, the gas is paid up until it runs out of gas or until it hits a revert (such as a require)
- Its best to revert as early as possible before accessing state variables or doing any computation
- Example:

`function claim() external {
        require(msg.sender == owner);
        // rest of logic
    }`

5. ***Short Circuiting***
- Soldity also has short circuiting just like Javascript ( || or &&)
- Having the less gas costly statement on the left will be a good way to save gas because there is a chance the other statement gets ignored
- Example: `require(block.timeStamp > 126548623 || whiteListed[msg.sender])` the left one cost less gas and if it returns true, the state variable "whiteListed" will not be read

6. ***Payable*** 
- Although it is not recommended to make ALL your functions payable, it can save gas.
- In a non payable function, Solidity checks if ether was sent(opcode CALLVALUE) and reverts if that is true, this causes more gas being used

7. ***Unchecked***
- Overflow happens when the sum of two uints returns a smaller value 
- Underflow is just the opposite, subtracting a number from another number returns a higher value
- Solidity 8.0 now has an overflow/underflow checker implemented 
- You can put your logic inside unchecked{} to avoid the extra opcodes and save gas but you must be very careful
- One ideal place to use unchecked is if you are using a counter and that counter increases by a small amount like 1
- Example: `unchecked{ counter++ }`
