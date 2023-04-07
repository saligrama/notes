# How the internet works

Base level: protocol stack, e.g.

```
(application)---datagram socket--->(user datagram protocol impl)---payload--->(internet datagram protocol impl)
--->(IP)--->(UDP)---datagram socket--->(application server)
```

Note not all of these are visible, because of encapsulation!