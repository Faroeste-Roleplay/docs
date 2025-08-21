# DataView

The DataView library provides a JavaScript-like interface for working with binary data in Lua, similar to the JavaScript ArrayBuffer and DataView APIs. It allows you to read and write different data types from binary strings with precise control over byte order (endianness).

## Overview

DataView consists of two main classes:

* `DataView`: For random access to binary data
* `DataStream`: For sequential reading of binary data

## DataView Class

### Constructor Methods

#### `DataView.ArrayBuffer`

Creates a new DataView with a buffer of the specified length.

**Parameters:**

* `length` (number): The size of the buffer in bytes

**Returns:** DataView instance

**Example:**

```lua
local buffer = DataView.ArrayBuffer(1024)
```

#### `DataView.Wrap`

Wraps an existing binary string in a DataView.

**Parameters:**

* `blob` (string): Binary data string

**Returns:** DataView instance

**Example:**

```lua
local binaryData = "\x00\x01\x02\x03"
local view = DataView.Wrap(binaryData)
```

### Instance Methods

#### `DataView:Buffer`

Returns the underlying binary data.

**Returns:** Binary string

#### `DataView:ByteLength`

Returns the length of the buffer in bytes.

**Returns:** Number of bytes

#### `DataView:ByteOffset`

Returns the current byte offset.

**Returns:** Byte offset

#### `DataView:SubView`

Creates a new DataView starting at the specified offset.

**Parameters:**

* `offset` (number): Starting byte offset

**Returns:** New DataView instance

## Data Types

The library supports various data types through the `DataView.Types` table:

| Type    | Code | Size (bytes) | Description             |
| ------- | ---- | ------------ | ----------------------- |
| Int8    | i1   | 1            | Signed 8-bit integer    |
| Uint8   | I1   | 1            | Unsigned 8-bit integer  |
| Int16   | i2   | 2            | Signed 16-bit integer   |
| Uint16  | I2   | 2            | Unsigned 16-bit integer |
| Int32   | i4   | 4            | Signed 32-bit integer   |
| Uint32  | I4   | 4            | Unsigned 32-bit integer |
| Int64   | i8   | 8            | Signed 64-bit integer   |
| Uint64  | I8   | 8            | Unsigned 64-bit integer |
| LuaInt  | j    | 8            | Lua integer             |
| UluaInt | J    | 8            | Unsigned Lua integer    |
| LuaNum  | n    | 8            | Lua number              |
| Float32 | f    | 4            | 32-bit float            |
| Float64 | d    | 8            | 64-bit float            |
| String  | z    | -1           | Null-terminated string  |

### Endianness

* `DataView.EndBig` (`">"`) - Big-endian byte order
* `DataView.EndLittle` (`"<"`) - Little-endian byte order

## Generated Methods

For each data type, the library automatically generates getter and setter methods:

### Get Methods

Pattern: `DataView:Get[Type]`

**Parameters:**

* `offset` (number): Byte offset in the buffer
* `endian` (boolean, optional): `true` for big-endian, `false` for little-endian

**Returns:** The value at the specified offset, or `nil` if out of bounds

**Examples:**

```lua
local view = DataView.ArrayBuffer(8)
local value = view:GetInt32(0, true)  -- Read big-endian 32-bit int at offset 0
local byte = view:GetUint8(4)         -- Read unsigned byte at offset 4
```

### Set Methods

Pattern: `DataView:Set[Type]`

**Parameters:**

* `offset` (number): Byte offset in the buffer
* `value`: Value to write
* `endian` (boolean, optional): `true` for big-endian, `false` for little-endian

**Returns:** The DataView instance (for method chaining)

**Examples:**

```lua
local view = DataView.ArrayBuffer(8)
view:SetInt32(0, 12345, true)    -- Write big-endian 32-bit int
view:SetUint8(4, 255)            -- Write unsigned byte
```

## Fixed-Size Methods

For working with fixed-size data:

### `DataView:GetFixedString`

Reads a fixed-length string.

### `DataView:SetFixedString`

Writes a fixed-length string.

## DataStream Class

DataStream provides sequential access to DataView data, automatically advancing the read position.

### Constructor

#### `DataStream.New`

Creates a new DataStream from a DataView.

**Parameters:**

* `view` (DataView): The DataView to read from

**Returns:** DataStream instance

**Example:**

```lua
local view = DataView.Wrap(binaryData)
local stream = DataStream.New(view)
```

### Reading Methods

For each data type, DataStream provides reading methods: Pattern: `DataStream:[Type]`

**Parameters:**

* `endian` (boolean, optional): `true` for big-endian, `false` for little-endian
* `align` (number, optional): Alignment value for offset advancement

**Returns:** The read value, or `nil` if end of stream

**Examples:**

```lua
local stream = DataStream.New(view)
local int32 = stream:Int32(true)     -- Read big-endian 32-bit int
local byte = stream:Uint8()          -- Read unsigned byte
local float = stream:Float32(false)  -- Read little-endian float
```

## Usage Examples

### Basic Usage

```lua
-- Create a buffer and write some data
local buffer = DataView.ArrayBuffer(16)
buffer:SetInt32(0, 0x12345678, true)    -- Write big-endian
buffer:SetFloat32(4, 3.14159, false)    -- Write little-endian
buffer:SetUint8(8, 255)                 -- Write byte

-- Read the data back
local intVal = buffer:GetInt32(0, true)
local floatVal = buffer:GetFloat32(4, false)
local byteVal = buffer:GetUint8(8)
```

### Sequential Reading with DataStream

```lua
local binaryData = "\x12\x34\x56\x78\x40\x49\x0f\xdb"
local view = DataView.Wrap(binaryData)
local stream = DataStream.New(view)

local int32 = stream:Int32(true)     -- Reads 0x12345678
local float32 = stream:Float32(true) -- Reads next 4 bytes as float
```

### Working with Strings

```lua
local buffer = DataView.ArrayBuffer(32)
buffer:SetString(0, "Hello World")
local text = buffer:GetString(0)
```

## Error Handling

* Methods return `nil` when trying to read/write beyond buffer bounds
* The library validates data type sizes against Lua's string.pack capabilities
* Automatic bounds checking prevents buffer overruns
