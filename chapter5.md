# 解决不断网无法访问spark问题

$ hostname

bogon

$ sudo vi /etc/hosts

127.0.0.1       localhost

255.255.255.255 broadcasthost

::1             localhost

127.0.0.1       bogon

$ ping bogon

PING bogon \(127.0.0.1\): 56 data bytes

64 bytes from 127.0.0.1: icmp\_seq=0 ttl=64 time=0.039 ms

64 bytes from 127.0.0.1: icmp\_seq=1 ttl=64 time=0.078 ms

64 bytes from 127.0.0.1: icmp\_seq=2 ttl=64 time=0.078 ms

64 bytes from 127.0.0.1: icmp\_seq=3 ttl=64 time=0.075 ms

重试成功

17/08/09 19:48:52 INFO Utils: Successfully started service 'sparkDriver' on port 65306.

