# Dell Latitude 7210 2-in-1
<br>
<img width="1275" height="765" alt="Desktop" src="https://github.com/user-attachments/assets/36b2737d-6e99-4d39-857c-a3612f268051" />
<img width="4032" height="1500" alt="4" src="https://github.com/user-attachments/assets/6618911b-0419-485b-901a-242391db9bd2" />

# Hardware Specification
```md
[CPU][Intel® Core™ i7-10810U]

[GPU][Intel® UHD Graphics 620]

[RAM][Samsung LPDDR3 8GB 2133Mhz] x 2 (16GB)

[Display][12.3 FHD 1920 x 1280 IPS] (Sharp: SHP14E0/LQ123N1)

[Touchscreen][WCOM48CE] (Wacom)

[Wi-Fi/BT Card][BCM94360NG NGFF M.2] (Swapped from Intel® Wi-Fi 6 AX201)

[SSD][WD SN530 M.2 2230 512GB] (Both macOS and Windows on the same drive)

[Audio][ALC295/ALC3254] (Layout-ID 77, Headphone jack requires ALCPlugFix to be functional)

[Pen][Dell Premium Active Pen PN579X]
```
Left side ports: 

  - Thunderbolt3/USB-C (Intel Titan Ridge; USB 3.1 Gen2 10Gbps; DisplayPort; Power Delivery) x2
  - microSD card reader (RTS525A PCIe) x1
  - Headset / Microphone (Universal Jack) x1
  - micro-SIM card x1
  - Smart card reader x1

Right side ports

  - USB-A (USB 3.1 Gen1 5Gbps) x1

# Working

- Restart/Shutdown

- S0 ModernStandby/Deep Idle (Idle/Lid close/Manual); Awful battery drain

- S3 Suspend to RAM/Normal Sleep (Idle/Lid close/Manual)
  
- S4 Hibernate/Standby (Immediate hibernation with Hibernatemode 25 or transition from S0 or S3 to S4 with hibernatemode 3)
  
- Wake (Wake using power button: S0, S3, S4 / Wake using external USB peripherals: S0 / Lid open: S0)

- Clamshell mode 

- Alps Trackpad(USB)/Wacom Touchscreen(I2C) with gestures

- Keyboard (PS/2; Function keys including Volume +/-, Brightness +/-)

- Volume Up/Down button on left side of tablet

- Pen PN579X: Limited functionality but pen hovering and top barrel button (right click) works.

- Display: 4K60FPS resolution via TB3/USB-C(DP-Alt-Mode) port/ Dim display on battery

- Built-in Cameras. (Front and rear RGB Cameras; though, much better image with using iPhone as webcam)

- Audio: Built-in speaker; Headphone jack requires ALCPlugFix to get sound

- Battery Status

- iMessage / Facetime 

- Airplay/Airdrop/Handoff/Continuity including iPhone as webcam (Requires natively supported Wi-Fi/BT card such as BCM94360NG)

- AirplaytoMac/Sidecar/Universal Control (Requires natively supported Wi-Fi/BT card such as BCM94360NG)

- Apple Music Lossless Audio ([unfairgva=4](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md); by spoofing board ID to iMacPro1,1(iGPU-less), lossless audio works in Apple Music; however, do note that spoofing board-ID to iMacPro1,1 on iGPU only system may break iGPU encoding/decoding functions and seemingly have issues with rps-control patch or Apple GuC firmware as the iGPU clock frequency and the power consumption can get stuck at max. This behavior is triggered when attempting video playback while having Apple Music play)
 
# Not Working

- DRM in Safari/Apple TV+ (Chrome browser will do)
- Fingerprint sensor
- Screen auto-rotation
  

# BIOS Settings

BIOS Revision: 1.40.0

- Enable Thunderbolt Boot Support - Enabled

- Thunderbolt -> No Security

- Secure Boot Enable -> Disabled

- Wake on AC -> Disabled
 
- Power On with Kickstand Open -> Disabled (Can cause wake issues from sleep if enabled)

- Sign of Life Indication -> Disable ALL (Required for resume after S3 sleep; If enabled, stuck at Dell logo after wake; does not affect S0 however)


# UEFI Setting Mod

This is not required to boot macOS at all and can be skipped, but is done to enable features that are otherwise would not be available. 

We can use [ModGrubShell](https://github.com/datasone/grub-mod-setup_var/releases) to modify settings (Can be used as a tool in OC)

For Dell BIOS IFR data extraction to find VarOffset, please refer to Dreamwhite's [guide](https://github.com/dreamwhite/bios-extraction-guide).

BIOS Version: 1.40.0

UEFI IFR settings

  |  	| Varstore 	| VarOffset	| Value |
  | :---: | :---: | :---: | :---: |
  |  CFG Lock | CpuSetup | 0x3E   | 0x1(Enable) 0x0(Disable) |
  |  Overclocking Lock | CpuSetup | 0xDA | 0x1(Enable) 0x0(Disable)|
  |  Configurable TDP Boot Mode | CpuSetup | 0x3F  | 0x0(Nominal) 0x1(Down) 0x2(Up)|
  |  TB Auto Switch | Setup | 0xA08   | 0x1(Enable) 0x0(Disable)|
  |  TB BIOS Enumeration Mode | Setup | 0xA09  | 0x1(Native) 0x0(BIOS Assist)|

- Disable CFG Lock -> (VarStore: CpuSetup, VarOffset: 0x3E)

       setup_var_cv CpuSetup 0x3E 0x1 0x0     (This will disable CFG Lock)
       setup_var_cv CpuSetup 0x3E 0x1 0x1     (Back to default)
  
         (setup_var_cv,VarStore,VarOffset,Size,Value)

Note: 

  - [Why disable CFG Lock?](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#what-is-cfg-lock)


# macOS

macOS: Big Sur ~ Ventura (Tested to work but older macOS like Sierra should also be possible with some spoofing)

  - macOS High Sierra - Catalina
    
    - SMBIOS -> MacBookPro15,2 (For High Sierra)
    - Set SecureBootModel -> Disabled
    - UEFI -> APFS -> MinDate and MinVersion -> -1
    - May require IGPU spoofing to older supported model. Refer to [WhateverGreen Manual](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md) for supported ig-platform-id
    - May require CPUID spoofing to older supported model
       
  - macOS Big Sur ~ Ventura

    - Works without making any changes to the posted EFI.

  - macOS Sonoma

    - To install or update to Sonoma 14.4+, SecureBootModel needs to be disabled.
    - To fix black screen on internal display for Sonoma+, Injecting [enable-backlight-registers-alternative-fix](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#fix-the-3-minute-black-screen-issue-on-kblcfl-platforms-running-macos-134-or-later) property is necessary
    - For macOS Sonoma+, Broadcom Wi-Fi has to depend on [OCLP](https://github.com/dortania/OpenCore-Legacy-Patcher) patch to be functional.

# OpenCore Config

Mostly follow OpenCore's Configuration.pdf and [Comet-Lake](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html) section of Dortania guide.

- ACPI

  ADD:

     - SSDT-AC.aml       (AC adapter device injection 6F, 04; necessary for SSDT-TB3HP.aml to be injected correctly when booting in TB3 BIOS Assist enumeration mode)
     - SSDT-ACOS.aml     (SSDT patch to allow Dell keyboard function keys to work, such as brightness +/-; use with BrightnessKeys.kext)
     - SSDT-AWAC.aml     (Disables AWAC clock and HPET; enables RTC to fix system clock)
     - SSDT-ALSD.aml     (Ambient Light sensor fix; works with SMCLightSensor kext; "Slightly dim the display on battery" feature becomes functional)
     - SSDT-Bridge.aml   (Injects missing IOReg names; cosmetic)
     - SSDT-DMAR.aml     (Modified DMAR table to prevent issues when enabling AppleVTD)
     - SSDT-EC-USBX.aml  (Fake embedded controller with USB power properties)
     - SSDT-LPCB.aml     (Injects fake devices, ARTC, DMAC, FWHD, and PMCR; cosmetic)
     - SSDT-MCHC.aml     (Injects fake MCHC device)
     - SSDT-PLUG.aml     (Injects plugin type 1 to load XCPM for CPU PMGMT; No longer required beginning with Monterey 12.3)
     - SSDT-PNLF.aml     (Fixes backlight control)
     - SSDT-USBP.aml     (ACPI USB Port Mapping; defines USB connector type for each USB ports at ACPI level)
     - SSDT-TB3HP.aml    (SSDT for Thunderbolt3 / Requires booting in Native Enumeration mode for hotplug)
     - SSDT-S0.aml       (Enables S0 deep idle)
  
   Delete:

    - Drop OEM DMAR Table 
   
   Quirks

    - FadtEnableReset -> Yes (Required to fix power button to function correctly (i.e putting system to sleep/fully wake system from sleep; not darkwake)
    - ResetHwSig -> Yes (Required to fix issues with waking from hibernation)


- Booter

   Patch

    - Skip Board-id check (Allows to boot macOS using unsupported SMBIOS)
      
- Device Properties

    - ALC295 audio layout-id <4D000000>
    - Intel UHD-620
      | Property 	| Data 	| Remarks 	|
      | :---: | :---: | :---: |
      |  AAPL,ig-platform-id | <00009B3E>   |   |
      |  device-id | <9B3E0000>	| |
      |  enable-backlight-registers-fix | <01000000> | Required to prevent black screen |
      |  enable-backlight-registers-alternative-fix | <01000000> | Required to prevent black screen (Sonoma+) |   
      |  enable-dpcd-max-link-rate-fix | <01000000> | Required to prevent kernel panic from a division-by-zero that happens with Sharp displays |
      |  disable-agdc | <01000000> | May be necessary to get HDMI audio when the display cables are routed through thunderbolt docking station such as WD19TBS |
      |  igfxfw | <02000000> 	| Forces loading Apple GuC firmware to increase performance; either use rps-control 1 or igfxfw |

         Please refer to [WhateverGreen Manual](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md) for more detailed explanation.


- Kernel

  Add:

     - VirtualSMC and its plugins
     - SMCBatteryManager (Required for correct battery status/No additional battery patching needed; all are 8-bit)
     - SMCLightsensor (Ambient Light Sensor; works with SSDT-ALS0.aml)
     - SMCDellSensors (Needed for Dell CPU fan monitoring)
     - Lilu/AppleALC/WhateverGreen 
     - [CPUFriend](https://github.com/acidanthera/CPUFriend)/[CPUFriendDataProvider](https://github.com/corpnewt/CPUFriendFriend) (Configure CPU powermanagement)
     - [VoltageShift](https://github.com/sicreative/VoltageShift) (To disable Intel Turbo Boost, undervolt, set Power Limit(PL1,PL2) and read or set MSR values; the kext itself won't do anything unless the settings are configured at OS level; Undervolting requires unlocking Overclock Lock in BIOS mod)
     - [RealtekCardReader](https://github.com/0xFireWolf/RealtekCardReader)/RealtekCardReaderFriend (For microSD card reader)
     - NVMeFix
     - VoodooPS2Controller/VoodooPS2Keyboard (For Keyboard)
     - [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)/VoodooInput/VoodooI2CServices/VoodooGPIO/VoodooI2CHID (For Trackpad/Touchscreen)
     - [BrightnessKeys.kext](https://github.com/acidanthera/BrightnessKeys) (For Brightness keys to work; use with SSDT-ACOS.aml)

  Emulate:

     CPUID spoofing is necessary for i7-10810U as macOS does not recognize its cpuid A0660. For this purpose, use i7-8665u cpuid 806EC

             Cpuid1Data -> EC060800 00000000 00000000 00000000
             Cpuid1Mask -> FFFFFFFF 00000000 00000000 00000000   
  
  Quirks:
  
     - AppleXcpmCfgLock -> Set to No if CFG Lock is disabled. Otherwise, enable this quirk.
     - CustomSMBIOSGuid -> If dual booting to other OSes other than macOS, set this to yes along with setting UpdateSMBIOSMode to "Custom" in PlatformInfo section.
     - DisableIoMapper -> No (Required to enable AppleVTD; enabling AppleVTD with base DMAR table can cause wake issue from hibernation. Either set this quirk to Yes or drop original DMAR table and inject modified SSDT-DMAR table.
     - DisableIoMapperMapping -> Yes (If enabling AppleVTD in Ventura+)
       
- Misc
  
   - Security -> ScanPolicy -> 0 (Set it to 2,687,747 if you wish to hide unnecessary entries)

   - SecureBootModel -> j223

- PlatformInfo -> Generic -> SystemProductName -> MacBookPro16,3
- PlatformInfo -> Generic -> UpdateSMBIOSMode -> Custom

- Theme -> [Blackosx/BSXm1](https://github.com/blackosx/BsxM1)

# ACPI USB Port Mapping

- HS01 -> Upper USB-C port
- HS02 -> Lower USB-C port
- HS03/SS03 -> Right side USB-A port
- HS04 -> Alps Trackpad 
- HS06 -> Front RGB Camera
- HS10 -> Bluetooth
- SS02 -> Rear RGB Camera

       * USB3 on USB-C ports are managed by Thunderbolt controller.
         If the TB controller is disabled, USB3 on USB-C ports are also disabled. 

# Misc

**Troubleshooting** (Hackintoshing & Refining)

  Display 
  -  Kernel Panic right before reaching desktop-> Injecting [enable-dpcd-max-link-rate-fix](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#fix-the-invalid-maximum-link-rate-issue-on-some-laptops-dell-xps-15-9570-etc) was required to resolve this.
  -  Black screen on built-in display -> Injecting [enable-backlight-registers-fix](enable-backlight-registers-fix ) was required to resolve this.
  -  [3-minute Black screen](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#fix-the-3-minute-black-screen-issue-on-kblcfl-platforms-running-macos-134-or-later) on built-in display for Sonoma+ -> Injecting enable-backlight-registers-alternative-fix was required to resolve this.
  -  Black screen on built-in display when rotating or connecting/disconnecting to/from external display -> This was due to using custom resolution to enable HIDPI resolution. Workaround is to set display resolution back to its default in prior and scale the resolution afterwards.
  -  Auto-rotation feature does not work. To manually rotate the screen, press and hold option/alt key(Ventura) and go to Display setting in System Settings for rotation option to show up in display settings. Alternatively, use [displayplacer](https://github.com/jakehilborn/displayplacer). One can create multiple bash scripts for each screen resolution and simply change resolution with a click or a tap.

      - Do note that when screen is rotated, scaling does not work. It will only work in default resolution.  Rotating screen requires first setting the resolution to default then rotate; otherwise, black screen. Doing this from the System Settings is time consuming and inconvenient. This is where displayplacer tool comes in handy to make it quite convenient and even more so when in tablet mode.

  - Adding Custom HiDPI resolutions

    To add custom HiDPI resolutions, we need to create a display override file to be placed in Library(Not System/Library) folder for Big Sur+. Refer to Rehabman's [guide](https://www.tonymacx86.com/threads/adding-using-hidpi-custom-resolutions.133254/) for the instruction and you may use this [Tool](https://codeclou.github.io/Display-Override-PropertyList-File-Parser-and-Generator-with-HiDPI-Support-For-Scaled-Resolutions/) for convenience. Manually added custom resolutions may not show up in Display preferences but they will show up as an available resolutions in third party display resolution managing apps like SwitchResX or displayplacer tool.

    Sample

 
    <img width="700" height="429" alt="Custom Res" src="https://github.com/user-attachments/assets/20e50e5a-e22d-4177-a4a1-ffdbc804aa60" />


    Display Override: [Displays.zip](https://github.com/user-attachments/files/22691416/Displays.zip)

    - DisplayProductID: 0x14e0    (5344)
    - DisplayVendorID: 0x4d10   (19728)

    Added Resolutions

        2562x1708
        1281x854 (In order to enable 1281x854 HiDPI, we also need to add a resolution that is x2 of that resolution which is 2562x1708)
        2400x1600
        1200x800 
        2700x1800
        1350x900
        2880x1920
        1440x960

       Note1: When making custom resolutions, make sure to keep the same aspect ratio as the default which 3:2
    
       Note2: Using HiDPI resolution may consume more power.

    
  Audio

  - ALC layout-id 77 works.
  - Unfortunately, the headphone jack on this laptop requires [AlcPlugFix](https://github.com/black-dragon74/ALCPlugFix-Swift) to be functional. (alc-verbs <01000000>, Node ID 0x19, Param 20, temporary disable SIP)
  - Boot-Chime not working in OpenCore boot menu -> Looking at the debug log, it's similar to issue described [here](https://github.com/acidanthera/bugtracker/issues/963). The solution is to upsample audio file to 48kHz. However, I usually don't like to use Boot-Chime as AudioDXE.efi driver causes a delay when the system posts and breaks audio in Windows (The fix for Windows can be found in OpenCore's Configuration.pdf). 

  CPU Powermanagement

  - Inject CPUFriend.kext with configured CPUFriendDataProvider.kext.
  - Thunderbolt3 BIOS Assist Enumeration (Compare to Native Enumeration mode, CPU package total power consumption is reduced by more than a watt at idle state (roughly from 3.3W to 1.7W)  
  - [VoltageShift.kext](https://github.com/sicreative/VoltageShift) to disable Intel Turbo Boost, undervolt, and set power limit to keep the temperature and fan noise down. Features like 
    BD PROCHOT may also be disabled via Voltageshift as it can read and write MSR values.
    <br>
    
    My Voltageshift settings as reference
    ```md
    |        [CPU][-150mv]
    |  [CPU Cache][-150mv]
    |        [GPU][-140mv]
    | [TurboBoost][OFF]
    |        [PL1][13W]
    |        [PL2][13W]
    ```
- Configurable TDP Boot Mode can be set via UEFI variable mod

  For i7-10810U 
  - Nominal: 1.1 GHz Base, 15W TDP
  - Down: 0.8 GHz Base, 12.5W TDP
  - Up: 1.6 GHz Base, 25W TDP
  
Thunderbolt Configuration

- Booting to Windows 10 sets the mode to Native Enumeration on next boot (OS Controls TB device, hotplug works)
- Booting to macOS sets the mode to BIOS Assist on next boot (BIOS handles the device enumeration, TB device work only if plugged before booting)

  By default, Auto Switch for BIOS Enumeration mode is enabled. (Not configurable from standard BIOS menu). This can be quite annoying with frequent dual-booting of macOS and Windows as the warning message "Thunderbolt PCIe Device Enumeration Mode Has Switched to XXXX" will pop up every time after having to boot to Windows or macOS. 

  To avoid this, we can disable Auto Switch via UEFI var mod and set the enumeration mode of choice depending on personal preference. As mentioned, Native mode has higher power consumption at idle but hotplug works where as BIOS Assist mode has lower power consumption at idle but will lose TB/USB-C USB3 hotplug.


- Disable Auto Switch -> (VarStore: Setup, VarOffset: 0xA08)

       setup_var_cv Setup 0xA08 0x1 0x0     (This will disable Auto Switch)
       setup_var_cv Setup 0xA08 0x1 0x1     (Back to default)
  
- BIOS Enumeration Mode -> VarStore: Setup, VarOffset: 0xA09)

       setup_var_cv Setup 0xA08 0x1 0x1     (Native) - default
       setup_var_cv Setup 0xA08 0x1 0x0     (BIOS Assist)


Sleep/Resume

  - Supports S0, S3, and S4
  - After S0 resume, TB3/USB-C USB3 hotplug function is lost, touchscreen remains functional.
  - After S0 resume, IGPU frequency may get stuck at max if igfxfw property is used.
  - After S3 resume, TB3/USB-C USB3 hotplug remain functional but touchscreen function is lost. (Workaround -> hibernation with hibernatemode 25 as needed)
  - After S4 resume, TB3 hotplug and touchscreen remain functional.
  - Disable lidwake for S3 and S4 as it can cause wake issues.
  - bluetoothd process with high cpu usage and power consumption after resume from sleep -> Workaround is to use [Bluesnooze](https://github.com/odlp/bluesnooze)

    Power Management Settings

        sudo pmset -a lidwake 0               # Disable lidwake to prevent wake issues for S3 and S4 
        sudo pmset -a proximitywake 0
        sudo pmset -a tcpkeepalive 0  

[Standby](https://support.apple.com/en-us/101363)(S4) / Hibernatemode  (Safe Sleep Arguments)

   From pmset Man page
   
  <img width="566" alt="Screenshot 2024-11-19 at 6 44 19 PM" src="https://github.com/user-attachments/assets/5b7ce353-513f-48c0-91cb-fb36767bf205">
<img width="570" alt="Screenshot 2024-11-19 at 6 44 30 PM" src="https://github.com/user-attachments/assets/90495778-b186-44cf-9235-aec31b67ac60">

   
   - Hibernatemode 0

     - pmset hibernatemode to 0 (Prevents system from hibernating)

   - Hibernatemode 3
     
      - pmset hibernatemode to 3
      - pmset standby to 1
      - Misc -> Boot -> Hibernatemode -> NVRAM
      - Misc -> Boot -> HibernateSkipsPicker -> Yes
      - Do not apply AppleRTC kernel patch if applied (patch to disable RTC wake scheduling)
      - Inject [HibernationFixup.kext](https://github.com/acidanthera/HibernationFixup)
      - Add boot-arg -> hbfx-ahbm=5 (Need this flag with value of at least 1 to put system in standby mode; refer to its [manual](https://github.com/acidanthera/HibernationFixup) for various configuration)
      - Set standbydelay time that suits your need in terminal (This sets the RTC alarm wake scheduling). The system will darkwake from normal sleep(S3) or deep idle(S0) as set by standbydelay argument then decides whether to transition to standby mode(This is when HibernationFixup.kext is needed). If transitions to standby, it saves current session to disk in var/vm/sleepimage and turns off some of the hardware systems to save power)
    
        
         <img width="996" height="72" alt="3" src="https://github.com/user-attachments/assets/b1abd3a2-3f61-4980-8230-03d5e0d99ef6" />



   - Hibernatemode 25 

      Hibernatemode 25 will immediately put system to standby preserving battery life.
      - pmset hibernatemode to 25
      - pmset standby to 1
      - Misc -> Boot -> Hibernatemode -> NVRAM
      - Misc -> Boot -> HibernateSkipsPicker -> Yes
    
        <img width="946" height="42" alt="25" src="https://github.com/user-attachments/assets/906eecd2-27d6-4e46-bd55-9bb41630a42c" />


 - Note 1: Make sure to drop original DMAR table and inject modified DMAR table to load AppleVTD as original DMAR table can cause wake issue from hibernation. Or, you may just disable AppleVTD by setting DisableIoMapper quirk to Yes. 
 - Note 2: ACPI -> Quirk -> ResetHwSig -> True is required to fix issues with wake from standby(S4).
   
 - Note 3: When in standby mode, the system could only wake via power button and it will continue from splash screen then straight into macOS if HibernateSkipsPicker is set to Yes

               
**Hardware**
   
Wi-Fi/Bluetooth

 - Comes with Intel® Wi-Fi 6 AX201 NGW -> Swapped with BCM94360NG module to enjoy Airplay/Airdrop/continuity features. (The wlan slot on this tablet is meant for one sided M.2 module and trying to fit in BCM94360NG can feel a bit tight. Ended up not tightening the screw all the way)

 - BCM94360NG speed issue with macOS

   You will find discussion about this problem from Acidenthera bugtracker #[1532](https://github.com/acidanthera/bugtracker/issues/1532) with possible workaround
   
   On Windows, internet speed works fine as it should with link speed up to 866 Mbps. However on macOS, speed becomes an issue. You'll notice that Tx rate is maxed at 434 Mbps with limited spatial stream to 1 (NSS=1) on cold boot. To gain its maximum capable speed, it seems that only workaround for now is to warm boot from Windows to macOS while having Broadcom Network Adapter driver version [7.35.295.2](https://www.catalog.update.microsoft.com/Search.aspx?q=Broadcom%20802.11n%20Network%20Adapter) (7/19/2015) installed. Note that this will only work with this specific version of driver.

   The particular BCM94360NG model that I have for this laptop has crash issues on windows 10 when the Wi-Fi drivers are installed. As a workaround, one of the USB-C ports needs to be occupied with USB device to prevent crash on Windows 10.


WWAN slot

- WWAN Slot can be used for additional storage device
      
# Geekbench 6

<img width="756" height="792" alt="Geekbench" src="https://github.com/user-attachments/assets/90603975-ba84-4542-9922-31cb4f7ec09c" />

# Resources

- [Apple Inc](https://www.apple.com/) for MacOS.
- [Acidanthera Team](https://github.com/acidanthera) for OpenCore Bootloader, its documentation, and KEXTS.
- [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) for thorough explanation.
- [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C) for the support of I2C devices.


Big thanks to Hackintosh community and those who contributed to make hackintosh possible from the beginning to the end!
