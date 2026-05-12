LuvitRED Function Node (Lua API)
Source: https://cloudgateuniverse.com/docs/nodes

The function node allows you to write custom code to perform advanced operations.
LuvitRED uses Lua as the scripting language for function nodes.

Linting
The Lua code style can be linted using luacheck by selecting the lint option in the flow node.

Message Handling
Messages injected by previous nodes are passed in as a Lua table called msg. Most messages will contain msg.payload, msg.topic, and msg.timestamp.
To send messages to the next node(s), the function should either return the messages or use the built-in functions send(msgs) and sendTo(output, msgs).
Optionally, the function can be called with a nil message on startup, which can be useful for initializing variables.

Returning Messages
- Single message object: Passed to nodes connected to the first output.
- Array of message objects: Passed to nodes connected to the corresponding outputs (index 1 to output 1, etc.).
- Array of arrays of message objects: Multiple messages passed to each output. For example, to pass three messages to output one, return { { m1, m2, m3 } }.
If nil is returned (either by itself or as an element of the array), no message is passed on.

Module Imports
To require modules in the function, create a module node. Then use the require function to have access to all functions in this module.

Startup
Select 'Call with empty message on startup'. The function will be called with msg set to nil after all modules and functions have been loaded and compiled but before any message has been sent. It is not possible to send any messages in this section of your code.

IMPORTANT: The function node can hang LuvitRED if the function executes an infinite loop. Be careful!

---

Available Lua Functions

Standard Lua Functions
assert(v [, message])
error(message [, level])
pcall(f [, arg1, arg2, ...])
tonumber(e [, base])
tostring(e)
type(v)
unpack(list)
ipairs(table)
pairs(table)
next(table [, index])
select(index, ...)

string.byte(str [, i [, j]])
string.char(...)
string.find(str, pattern)
string.format(fmt, ...)
string.gmatch(str, pattern)
string.gsub(str, pattern, repl [, n])
string.len(str)
string.lower(str)
string.match(str, pattern [, init])
string.rep(str, n)
string.reverse(str)
string.sub(str, i [, j])
string.upper(str)

table.insert(table, [pos, ] value)
table.remove(table [, pos])
table.concat(table [, sep [, i [, j]]])
table.sort(table [, comp])
table.maxn(table)

math.abs
math.ceil
math.floor
math.huge
math.exp
math.log
math.log10
math.max
math.min
math.modf
math.pi
math.pow
math.sqrt
math.random

os.date
os.difftime
os.time

Timer Functions
setTimeout(ms_time, func)  -- returns time_obj
clearTimeout(time_obj)
setInterval(ms_time, func) -- returns time_obj
clearInterval(time_obj)

LuvitRED Functions
createMessage([topic [, payload]]) -- creates a new message
msg:clone()                        -- creates a copy of 'msg'
send(messages)                     -- sends message(s)
sendTo(output, messages)           -- send message(s) to numbered output

statusIdle(str)    -- sets node status to idle with str
statusActive(str)  -- sets node status to active with str
statusError(str)   -- sets node status to error with str

require(module)    -- returns module from module node
p(...)             -- dumps arguments into debug window
throw(code_str, fmt, ...)  -- creates error for catch node

Other Utility Functions
generateUUID()              -- returns random UUID string
randomBytes(len)            -- OpenSSL random bytes
sha256(str)                 -- SHA256 hash of str
sha1(str)                   -- SHA1 hash of str
md5(str)                    -- MD5 hash of str
hmacSha256(str, key)        -- SHA256 HMAC of str
hmacSha1(str, key)          -- SHA1 HMAC of str

base64encode(str)           -- base64 encode of str
base64decode(str)           -- base64 decode of str
stohex(str [, reverse])     -- converts binary to HEX string (optionally reverse)
hextos(str [, reverse])     -- converts HEX string to binary (optionally reverse)
json.encode(table)
json.decode(string)

getTimeMs()                 -- current time in milliseconds

Pack/Unpack Functions
lpack(fmt, arg1, arg2, ...)  -- pack args into str using fmt
lunpack(data, fmt [, init])  -- unpack data using fmt

Pack format codes:
  z - zero terminated string
  p - string preceded by length byte
  P - string preceded by length word
  a - string preceded by length size_t
  A - string
  f - float
  d - double
  c - char
  u - int8_t
  b - uint8_t
  h - int16_t
  H - uint16_t
  i - int32_t
  I - uint32_t
  l - long
  L - unsigned long
  m - int64_t
  M - uint64_t
  n - signed variable integer 1-8 bytes (e.g. n6)
  N - unsigned variable integer 1-8 bytes
  < - little endian
  > - big endian
  = - native endian

External References
- Bit functions: LuaJIT bit operations module
- Underscore functions: underscore.lua
- lpack functions: lpack documentation
