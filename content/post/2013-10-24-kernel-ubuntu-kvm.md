+++
title = "kernel? ubuntu? kvm?"
date = "2013-10-24T11:28:00+08:00"
+++
不知道是誰的 bug, 新裝的 3.5.0-23-generic Kernel (Ubuntu),
在 remote 做 mongoimport 時會噴出錯誤, 即使換成 ext4 也一樣:
```
[656289.998839] ata1.00: exception Emask 0x0 SAct 0x0 SErr 0x0 action 0x6 frozen
[656289.998910] ata1.00: failed command: WRITE DMA EXT
[656289.998953] ata1.00: cmd 35/00:f8:f0:5c:45/00:03:02:00:00/e0 tag 0 dma 520192 out
[656289.998953]          res 40/00:01:00:00:00/00:00:00:00:00/e0 Emask 0x4 (timeout)
[656289.999065] ata1.00: status: { DRDY }
[656289.999226] ata1: soft resetting link
[656290.152502] ata1.01: NODEV after polling detection
[656290.153668] ata1.00: configured for MWDMA2
[656290.153683] ata1.00: device reported invalid CHS sector 0
[656290.153734] sd 0:0:0:0: [sda]  
[656290.153737] Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE
[656290.153740] sd 0:0:0:0: [sda]  
[656290.153742] Sense Key : Aborted Command [current] [descriptor]
[656290.153747] Descriptor sense data with sense descriptors (in hex):
[656290.153749]         72 0b 00 00 00 00 00 0c 00 0a 80 00 00 00 00 00 
[656290.153759]         00 00 00 00 
[656290.153764] sd 0:0:0:0: [sda]  
[656290.153767] Add. Sense: No additional sense information
[656290.153769] sd 0:0:0:0: [sda] CDB: 
[656290.153771] Write(10): 2a 00 02 45 5c f0 00 03 f8 00
[656290.153780] end_request: I/O error, dev sda, sector 38100208
[656290.153863] Buffer I/O error on device dm-2, logical block 1646
[656290.153927] lost page write due to I/O error on dm-2
[656290.153968] Buffer I/O error on device dm-2, logical block 1647
[656290.154032] lost page write due to I/O error on dm-2
[656290.154060] Buffer I/O error on device dm-2, logical block 1648
[656290.154125] lost page write due to I/O error on dm-2
[656290.154151] Buffer I/O error on device dm-2, logical block 1649
[656290.154227] lost page write due to I/O error on dm-2
[656290.154254] Buffer I/O error on device dm-2, logical block 1650
[656290.154319] lost page write due to I/O error on dm-2
[656290.154323] Buffer I/O error on device dm-2, logical block 1651
[656290.154387] lost page write due to I/O error on dm-2
[656290.154414] Buffer I/O error on device dm-2, logical block 1652
[656290.154481] lost page write due to I/O error on dm-2
[656290.154509] Buffer I/O error on device dm-2, logical block 1653
[656290.154574] lost page write due to I/O error on dm-2
[656290.154600] Buffer I/O error on device dm-2, logical block 1654
[656290.154663] lost page write due to I/O error on dm-2
[656290.154689] Buffer I/O error on device dm-2, logical block 1655
[656290.154753] lost page write due to I/O error on dm-2
[656290.155099] ata1: EH complete
[656290.155121] REISERFS abort (device dm-2): Journal write error in flush_commit_list
```
依經驗, 要是 file system seek 沒有 bad sector, 通常換條排線就沒事了.
但這是 VM 阿! 不知道是 kernel/ubuntu/kvm 誰的問題, 總之把 NCQ 取消掉就正常了.
```
GRUB_CMDLINE_LINUX="libata.force=noncq"
```