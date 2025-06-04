epictreasure
```bash
docker run --privileged -v ${PWD}:/root/host-share --rm -it --workdir=/root/host-share rjwalls/epictreasure tmux
```

epictreasure openocd edition
```bash
docker run --privileged -v ${PWD}:/root/host-share --rm -it --workdir=/root/host-share kcjesse/epicopenocd tmux
```


epictreasure networks edition
```shell
docker run --privileged -v ${PWD}:/root/host-share --rm -it --workdir=/root/host-share  epictreasure/networks tmux
```

dwarf-fortress
```shell
docker run -e 'DISPLAY_DIMENSIONS'='1440x763' -v '/Users/katherinejesse/df/':'/df_linux/data/save/':'rw' -p 9650:8080 --security-opt=seccomp=unconfined -t dwarffortress
```


MQP temp
- Make sure they 
- What would require them to spin up a gdb server?
- 