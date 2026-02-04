# Troubleshooting - Hyper-V VM Hibák

## Hiba: "The boot loader failed" - ISO nem bootol

### Tünetek:
- VM elindul, de nem tölt be operációs rendszert
- "Virtual Machine Boot Summary" hibaüzenet
- "SCSI DVD - The boot loader failed"
- "No operating system was loaded"

### Okok:
1. ISO nincs csatolva a VM-hez
2. ISO rossz helyen van csatolva (IDE vs SCSI)
3. Boot sorrend nem megfelelő (Generation 2 VM esetén)
4. ISO sérült vagy nem megfelelő

---

## Megoldás: ISO csatolása és boot sorrend beállítása

### 1. lépés: VM leállítása
1. Hyper-V Manager-ben jobb klikk a VM-re
2. Válaszd: **Turn Off** (ha fut)

### 2. lépés: VM Settings megnyitása
1. Jobb klikk a VM-re: **Settings**
2. Bal oldali menü: **SCSI Controller** vagy **IDE Controller**

### 3. lépés: DVD Drive ellenőrzése

#### Ha Generation 2 VM (UEFI):
1. Bal oldal: **SCSI Controller**
2. Kattints a **DVD Drive**-ra (ha van)
3. Jobb oldal: **Image file (.iso):** rész
4. Kattints: **Browse**
5. Válaszd ki a Windows Server ISO fájlt
6. Kattints: **Apply**

#### Ha Generation 1 VM (Legacy BIOS):
1. Bal oldal: **IDE Controller 1**
2. Válaszd: **DVD Drive**
3. Jobb oldal: **Image file:**
4. Browse > Válaszd ki az ISO-t
5. Apply

### 4. lépés: Boot Order beállítása (Generation 2 VM esetén)

1. VM Settings bal oldal: **Firmware**
2. **Boot order** lista:
   - **DVD Drive** legyen az első helyen
   - Ha nincs a listában, add hozzá
3. Használd a **Move Up** gombot, hogy a DVD Drive felülre kerüljön
4. Kattints: **Apply** majd **OK**

### 5. lépés: VM újraindítása
1. Jobb klikk a VM-re
2. **Start**
3. **Connect** (hogy lásd a képernyőt)
4. Most már boot-olnia kell az ISO-ból

---

## További tippek

### ISO ellenőrzése
- Ellenőrizd, hogy az ISO fájl nem sérült
- Próbáld újra letölteni, ha szükséges
- Nézd meg a fájl méretét (kb. 5-6 GB kell legyen)

### Memory/RAM probléma
- Ha 16 GB RAM van a gépen és 8 GB-ot adsz a VM-nek, az szűkös lehet
- Csökkentsd 4 GB-ra a VM RAM-ját
- Kapcsold be a **Dynamic Memory**-t

### Generation váltás
- Ha Generation 2 nem működik, próbáld Generation 1-gyel
- Generation 1: Legacy BIOS (IDE Controller)
- Generation 2: UEFI (SCSI Controller)

---

## PowerShell megoldás (alternatív módszer)

```powershell
# VM DVD drive beállítása
Set-VMDvdDrive -VMName "WinServer22" -Path "E:\ISOs\WindowsServer2022.iso"

# DVD drive hozzáadása, ha nincs
Add-VMDvdDrive -VMName "WinServer22" -Path "E:\ISOs\WindowsServer2022.iso"

# Boot order ellenőrzése (Gen 2 VM)
Get-VMFirmware -VMName "WinServer22" | Select-Object -ExpandProperty BootOrder

# DVD Drive-ot első helyre rakni
$dvd = Get-VMDvdDrive -VMName "WinServer22"
Set-VMFirmware -VMName "WinServer22" -FirstBootDevice $dvd
```

---

**Leggyakoribb megoldás**:
- VM Settings > SCSI Controller > DVD Drive > Browse > Válaszd az ISO-t > Apply > OK
- VM Settings > Firmware > Boot order > DVD Drive legyen első > Apply > OK
- Start VM

**Dátum**: 2025-02-04
