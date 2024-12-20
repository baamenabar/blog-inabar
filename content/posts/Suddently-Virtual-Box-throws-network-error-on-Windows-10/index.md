title: Suddently Virtual Box throws network error on Windows 10
classes: language-markup
date: 2017-08-17T22:20:44-02:00

# Suddently Virtual Box throws network error on Windows 10

I've been working with virtuabox on my Windows machine (Vagrant and Docker environments).  
When I tried to start a machine (munich) I get a criptic network error.

```sh 
docker-machine start munich\
Starting "munich"...
(munich) Check network to re-create if needed...
Unable to start the VM: C:\Program Files\Oracle\VirtualBox\VBoxManage.exe startvm munich --type headless failed:
VBoxManage.exe: error: Failed to open/create the internal network 'HostInterfaceNetworking-VirtualBox Host-Only Ethernet Adapter #6' (VERR_INTNET_FLT_IF_NOT_FOUND).
VBoxManage.exe: error: Failed to attach the network LUN (VERR_INTNET_FLT_IF_NOT_FOUND)
VBoxManage.exe: error: Details: code E_FAIL (0x80004005), component ConsoleWrap, interface IConsole

Details: 00:00:01.748287 Power up failed (vrc=VERR_INTNET_FLT_IF_NOT_FOUND, rc=E_FAIL (0X80004005))
```

After much googling and reading, the solution is:

Go to your **control panel** &gt; **Network and shred resources center** &gt; **Network connections**

Here you will see a bunch of network connections icons. Amongst them: `VirtualBox Host-Only Network #6`

Double click it and open it's properties. A new window will show up, with a list. The item **VirtualBox NDIS 6 Bridged Networking Driver** Will be unchecked. Check it and click OK.

Try to start your virtual machine again, it should work now.
