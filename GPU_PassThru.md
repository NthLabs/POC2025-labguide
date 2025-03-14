List GPUs installed

```
$ lspci -nn | grep -i nvidia

8a:00.0 3D controller [0302]: NVIDIA Corporation Device [10de:26b9] (rev a1)
```

Configure grub (Intel CPU + NVIDIA)

```
$ sudo vi /etc/default/grub
```

> GRUB_CMDLINE_LINUX="intel_iommu=on iommu=pt vfio-pci.ids=10de:26b9" 

Configure linux modules

```
$ sudo vi /etc/modules
```

> vfio-pci

Update grub

```
$ sudo update-grub
$ sudo reboot
```

Verify isolation

```
$ lspci -nnv
Kernel driver in use: vfio-pci
```
