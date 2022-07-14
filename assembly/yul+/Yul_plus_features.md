# Yul+ Features

- Everything related to original yul
- Enums 
- Constants 
- ABI Ethereum signature generation
- Booleans
- Safe math by default 
- Memory structures (mstruct)

## Enums
restrict the variable to one of several predefined values

```javascript
enum Colors (
   Red,  // 0
   Blue, // 1
   Green // 2
)
```

## Constants

```javascript
// Constant someConst will equal 1
const someColor := Colors.Blue
// Constant someBool will equal 0x1
const someBool := true
```

## ABI Ethereum signature generation

```javascript
// someVar will equal 4 byte method signature 0x6057361d
let someVar := sig”function store(uint256 val)”
// someTopic will equal 32 byte topic hash 0x69404ebde4a368ae324ed310becfefc3edfe9e5ebca74464e37ffffd8309a3c1
let someTopic := topic”event Store(uint256 val)”
```

## Safe math
Overflow/underflow protection for addition, subtraction, multiplication

```javascript
let someVar := add(3, sub(4, 2))
// will compile to this, with safeAdd, safeSub methods injected
let someVar := safeAdd(3, safeSub(4, 2))
```

```javascript
// Constant someConst will equal 1
const someColor := Colors.Blue
// Constant someBool will equal 0x1
const someBool := true
```

## Memory Structures
Memory structures enable better handling of pre-existing in-memory structures.

```javascript
mstruct Identifier ( [ property, ... ] )
```

Note, `mstruct` properties allow for data chunk sizes up to 32 bytes only.


## Example: Yul+ SimpleStore Contract

```javascript
object “SimpleStore” {
   code {
      datacopy(0, dataoffset(“Runtime”), datasize(“Runtime”))
      return(0, datasize(“Runtime”))
   }
   object “Runtime” {
      code {
         calldatacopy(0, 0, 36)                       // copy calldata into memory
         mstruct Calldata(                            // mstruct describes calldata
            sig: 4,
            val: 32
         )
         switch Calldata.sig(0)                       // get signature at positive zero
         
         case sig”function store(uint256 val)” {      // store method
            sstore(0, Calldata.val(0))
         }
         case sig”function get() returns (uint256)” { // get method
            mstore(100, sload(0))
            
            return (100, 32)
         }
      }
   }
}
```
