
Standard IP access list 1
    10 permit 192.168.0.0, wildcard bits 0.0.0.63
    20 deny 192.168.1.0, wildcard bits 0.0.0.255
    30 permit any

In this case:

- Rule **10** is evaluated first.
- Rule **20** is evaluated if rule 10 does not match.
- Rule **30** is evaluated last.

In summary, the sequence number represents the **position of the rule** within the access list and the lower the sequence number, the higher priority.