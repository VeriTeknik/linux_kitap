# ip

```bash
ip addr show
```

```bash
ip addr add 192.168.16.5 dev eth0
```


```bash
ip addr del 192.168.16.5 dev eth0
```

```bash
ip link set eth0 up
```

```bash
ip link set eth0 down
```

```bash
ip route show
```

```bash
ip route add 10.2.19.0/24 via 192.168.16.1 dev eth0
```

```bash
ip route del 10.2.19.0/24
```


```bash
ip route add default via 192.168.16.1
```