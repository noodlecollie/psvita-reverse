The eventual objective, if I get that far, is to be able to swap out the PS Vita's 3G modem for a more modern 4G or 5G one. After talking to people in the Discord channel, I understand that this will likely require reverse-engineering the existing modem driver (`bbmc.skprx`) and replacing its kernel module with a new one.

To do this, I first need to work out how to open `bbmc.skprx` in Ghidra. https://github.com/CreepNT/VitaLoaderRedux seems like it should be useful for this, and reads files in the ELF-PRX format, which I assume is the Vita's variant of ELF.

A firmware dump that feels like it might be useful is https://files.olebeck.com/firmware/vita/decrypted/external/devkit/3.730.011/PUP_dec/. This seems to be for a devkit, and helpfully the encrypted packages have been extracted. `psp2swu.elf` file might be a good place to start, which I'm assuming stands for "PSP to Software Update", perhaps from having been built on the PSP's framework.

Importing this went OK, but gave the following at the end:

```
Linking the External Programs of 'psp2swu.elf' to imported libraries...
  [libshellsvc.suprx] -> not found in project
  [driver_us.suprx] -> not found in project
  [appmgr.skprx] -> not found in project
  [display.skprx] -> not found in project
  [touch.skprx] -> not found in project
  [bt.skprx] -> not found in project
  [ctrl.skprx] -> not found in project
  [libgxm_es4.suprx] -> not found in project
  [audio.skprx] -> not found in project
  [regmgr.skprx] -> not found in project
  [libkernel.suprx] -> not found in project
  [iofilemgr.skprx] -> not found in project
  [modulemgr.skprx] -> not found in project
  [processmgr.skprx] -> not found in project
  [sysmem.skprx] -> not found in project
  [threadmgr.skprx] -> not found in project
  [power.skprx] -> not found in project
  [libhttp.suprx] -> not found in project
  [update_mgr.skprx] -> not found in project
  [vshbridge.skprx] -> not found in project
  [ss_mgr.skprx] -> not found in project
```

These are the kernel modules that the executable would use, and they are not present in the online directory. I'm assuming that these are the libraries that would need to be present on the PS Vita in order to run this update package, and that the libraries present in the update itself are somewhere in the associated binaries.

---

False start! After asking in the Discord, I've been pointed towards https://github.com/LiEnby/psvita-elfs, which should hopefully contain everything I need. https://wiki.henkaku.xyz/vita/SceBbmc details the exported functions of the module.
