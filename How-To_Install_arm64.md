# How to Intstall on Mac

Usually Virtual Box won't work on newer Macs because they use ARM architecture. But no worries you can still use our project! I used UTM for this, and UTM works because it’s designed for Apple Silicon and uses Apple’s Hypervisor framework (and QEMU) to run ARM-based systems efficiently.

You can download it from here https://mac.getutm.app/

After you downloaded it you need to choose right iso picture. Choose ARM version of Debian, not the standard INTEL one! 

https://www.debian.org/distrib/

This one: 64-bit ARM (qcow2, raw)

<img width="698" height="557" alt="Näyttökuva 2026-04-08 kello 12 03 27" src="https://github.com/user-attachments/assets/e692b1f8-5a31-4a7f-b7b2-0c405473eccf" />

After clickin it, it should start download.

### UTM

After downloading those files, you can start to power up a VM.

Open UTM app and click "New Virtual Machine" (Top left corner)


<img width="647" height="651" alt="Näyttökuva 2026-04-08 kello 12 08 02" src="https://github.com/user-attachments/assets/7a38f234-3e6b-49d4-8305-0f4310d473d8" />

Next click "Virtualize" (top one)

<img width="436" height="487" alt="Näyttökuva 2026-04-08 kello 12 08 30" src="https://github.com/user-attachments/assets/15cdbd21-22fb-4ac3-b138-712e73d09296" />

Then you get to choose operating system, so choose Linux.

<img width="442" height="494" alt="Näyttökuva 2026-04-08 kello 12 09 58" src="https://github.com/user-attachments/assets/1e2750b5-a3f6-4936-8193-f642c1e90569" />

This is the part where you need to use ISO picture, choose "Browse..."

<img width="448" height="499" alt="Näyttökuva 2026-04-08 kello 12 10 56" src="https://github.com/user-attachments/assets/006d3d85-6c3d-4428-8519-592e627e8758" />

And click the debian iso picture.

<img width="675" height="23" alt="Näyttökuva 2026-04-08 kello 12 15 08" src="https://github.com/user-attachments/assets/36023ac3-61d4-49e7-8686-503220c28159" />


After this you should see a screen where you can choose base memory and CPU's. 

We been running this with +8000MB and 2-3 CPU's, and it is working well.


<img width="444" height="509" alt="Näyttökuva 2026-04-08 kello 12 19 32" src="https://github.com/user-attachments/assets/12adbd60-c68b-412a-9897-13f5a3485374" />

Then choose virtual Hard disk is the size of the disk for the VM. 

You should have at least 32GB to run this smoothly. With 20-25GB's it is lagging a bit.  

<img width="436" height="497" alt="Näyttökuva 2026-04-08 kello 12 22 15" src="https://github.com/user-attachments/assets/735da676-9ccb-4ddd-9c9e-8f5dc61095e6" />

