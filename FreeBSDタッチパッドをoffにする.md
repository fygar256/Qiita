


**************************************************


FreeBSDタッチパッドをoffにする


**************************************************



xinput listで、タッチパッドのidを調べます。

```
$ xinput list
⎡ Virtual core pointer                    	id=2	[master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer              	id=4	[slave  pointer  (2)]
⎜   ↳ System mouse                            	id=6	[slave  pointer  (2)]
⎜   ↳ GlidePoint                              	id=11	[slave  pointer  (2)]
⎜   ↳ Logitech USB Receiver                   	id=13	[slave  pointer  (2)]
⎣ Virtual core keyboard                   	id=3	[master keyboard (2)]
    ↳ Virtual core XTEST keyboard             	id=5	[slave  keyboard (3)]
    ↳ System keyboard multiplexer             	id=7	[slave  keyboard (3)]
    ↳ Power Button                            	id=8	[slave  keyboard (3)]
    ↳ Sleep Button                            	id=9	[slave  keyboard (3)]
    ↳ AT keyboard                             	id=10	[slave  keyboard (3)]
    ↳ Logitech USB Receiver
```

ここでは、11番につながっているので、11番をoffにします

```
$ xinput set-prop 11 "Device Enabled" 0
```
