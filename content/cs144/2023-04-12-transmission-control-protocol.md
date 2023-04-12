# Transmission Control Protocol

Goal: turn byte-stream `push`, `pop`, `peek` interfaces into idempotent operations

TCP sender message (aka lab checkpoint 1):

```
first_index: 0
data: "abcd"
FIN: false or true
```