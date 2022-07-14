# Yul
Yul objects are used to group named sections of code and data.
The datasize, dataoffset and datacopy functions can be used to access
to these sections from the code. Hexadecimal strings can be used
to specify data in hexadecimal encoding, ordinary strings in native encoding.
For datacopy will refer to its binary representation.

> - A contract consists of a single object with sub-objects representing the code to deploy or other contracts it can create.
> 
> - "code" is the executable code of the object.
> 
> - Each (other) named object or data section is serialized and made available to the special datacopy/dataoffset/datasize builtins.

## Examples:


A basic Yul contract with a constructor and runtime

```javascript
object "EmptyContract" {
  code {
    // Your constructor code
    datacopy(0, dataoffset("Runtime"), datasize("Runtime"))
    return(0, datasize("Runtime"))
  }
  object "Runtime" {
    code {
       // Your runtime code
    }
  }
}
```

Handling calldata

```javascript
// copy calldata to memory
// this copies 36 bytes of transaction calldata to memory position 0
calldatacopy(0, 0, 36)
```

Managing memory

```javascript
// store and read memory
// store 0xaa at memory position 100
mstore(100, 0xaa)
// load 32 byte chunk from memory position 100 and assign to someVar
let someVar := mload(100)
```

Hashing

```javascript
// hash memory position 0 to 0+32, assign result to someHash
let someHash := keccak256(0, 32)
```

State storage

```javascript
// store value 0xaa in state storage slot 3
sstore(3, 0xaa)
// get value from state storage 3 and assign to someVar
let someVar := sload(3)
```

Functions, conditions, loops, and switches

```javascript
// Functions and conditions
function someMethod(someVar, someOther) -> someResult {
   if eq(someVar, someOther) {
      someResult := 0x45
   }
}
// Loops
for { let i := 0 } lt(i, 100) { i := add(i, 1) } {
   // some loop code
}
// Switches
switch someVar
case 0 {
   // when someVar == 0
}
case 1 {
   // when someVar == 1
}
default {
   // default
}
```

`datacopy(t, f, s)` - copy **s** bytes from the code at position **f** into memory at position **t**

`datasize` and `dataoffset` can only take string values(names of other objects) and return
 `datasize` and `dataoffset` in the data area
