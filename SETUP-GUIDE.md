# Hyper-V Windows Server Setup Guide

## 1. lépés: Windows Server ISO letöltése

### Opció A: Microsoft Evaluation Center (Ajánlott kezdőknek)

1. Menj a Microsoft Evaluation Center-be:
   ```
   https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022
   ```

2. Válaszd a **Windows Server 2022** vagy **2019** verziót
3. Töltsd le az **ISO** fájlt (körülbelül 5-6 GB)
4. Nem kell regisztráció, 180 napos evaluation verzió

### Opció B: MSDN / Visual Studio Subscription
- Ha van előfizetésed, onnan töltheted le a teljes verziót

---

## 2. lépés: Hyper-V Manager megnyitása

1. Nyomj **Win + S** billentyűkombinációt
2. Írd be: `Hyper-V Manager`
3. Nyisd meg az alkalmazást

**Vagy:**
- Nyomj **Win + R**
- Írd be: `virtmgmt.msc`
- Enter

---

## 3. lépés: Új virtuális gép létrehozása

### A. Hyper-V Manager ablakban

1. Jobb oldali panelen kattints: **New > Virtual Machine**
2. **New Virtual Machine Wizard** elindul

### B. Virtual Machine beállítások

#### Alapbeállítások:
- **Name**: `WinServer2022-Lab` (vagy tetszőleges név)
- **Location**: Alapértelmezett vagy egyéni (pl. `D:\Hyper-V\VMs\`)

#### Generation (Generáció):
- Válaszd: **Generation 2** (újabb, UEFI támogatás)

#### Memory (Memória):
- **Startup RAM**: Minimum **2048 MB** (2 GB)
- Ajánlott: **4096 MB** (4 GB)
- Pipáld be: **Use Dynamic Memory**

#### Networking (Hálózat):
- Ha van Virtual Switch, válaszd ki (pl. **Default Switch**)
- Ha nincs, később létrehozhatod

#### Virtual Hard Disk (Virtuális lemez):
- **Create a virtual hard disk**
- **Size**: Minimum **32 GB**, ajánlott **60 GB**
- **Location**: Alapértelmezett vagy egyéni

#### Installation Options:
- Válaszd: **Install an operating system from a bootable image file**
- **Browse**: Válaszd ki a letöltött Windows Server ISO fájlt

### C. Finish
- Kattints a **Finish** gombra
- A VM létrejött, de még nem indítottad el

---

## 4. lépés: Windows Server telepítés

1. Hyper-V Manager-ben jobb klikk a VM-re
2. Válaszd: **Connect**
3. Új ablak nyílik meg (Virtual Machine Connection)
4. Kattints: **Start** gomb (zöld play ikon)

### Telepítési folyamat:
1. **Nyelv kiválasztása**: English (vagy magyar, ha elérhető)
2. **Install now**
3. **Válaszd a verziót**:
   - **Windows Server 2022 Standard Evaluation (Desktop Experience)** - GUI-val
   - vagy **Datacenter Evaluation (Desktop Experience)**
4. **License terms**: Fogadd el
5. **Installation type**: **Custom: Install Windows only**
6. **Partíció**: Válaszd ki az egyetlen virtuális lemezt, kattints **Next**
7. Telepítés indul (5-15 perc)
8. **Újraindítás után**: Állítsd be az Administrator jelszót

---

## 5. lépés: Alapvető konfiguráció telepítés után

1. Jelentkezz be Administrator fiókkal
2. **Server Manager** automatikusan elindul
3. Állítsd be:
   - Számítógép nevet (Rename PC)
   - Időzóna (Time zone)
   - Windows Update (opcionális)

---

## 6. lépés: Checkpoint (Pillanatkép) készítése

**Miért fontos?**
- Biztonságos visszaállítási pont
- Ha elromlik valami, azonnal visszaállhatsz
- Ideális teszteléshez

### Checkpoint létrehozása:
1. Hyper-V Manager-ben jobb klikk a VM-re
2. Válaszd: **Checkpoint**
3. Adj nevet: `Clean Install - 2025-02-04`
4. Kész!

### Checkpoint visszaállítása:
1. Hyper-V Manager: VM > Checkpoints panel (alul)
2. Jobb klikk a checkpoint-ra
3. Válaszd: **Apply** vagy **Restore**

---

## Hasznos parancsok (PowerShell)

```powershell
# Összes VM listázása
Get-VM

# VM elindítása
Start-VM -Name "WinServer2022-Lab"

# VM leállítása
Stop-VM -Name "WinServer2022-Lab"

# Checkpoint készítése
Checkpoint-VM -Name "WinServer2022-Lab" -SnapshotName "Clean Install"

# Checkpoint visszaállítása
Restore-VMSnapshot -Name "Clean Install" -VMName "WinServer2022-Lab" -Confirm:$false
```

---

## Következő lépések

1. Telepítsd a vendég integrációs szolgáltatásokat (ha szükséges)
2. Hozz létre több checkpoint-ot különböző konfiguráció után
3. Teszteld a visszaállítást
4. Dokumentáld screenshotokkal a folyamatot
5. Készíts részletes README-t a projekthez

---

## Troubleshooting

### VM nem indul el
- Ellenőrizd, hogy a Hyper-V szolgáltatás fut-e
- Nézd meg, hogy az ISO csatlakoztatva van-e

### Lassú teljesítmény
- Növeld a RAM-ot (4 GB vagy több)
- Ellenőrizd, hogy a Dynamic Memory be van-e kapcsolva
- CPU magok: 2 vagy több

### Hálózati kapcsolat nem működik
- Hyper-V Manager: Virtual Switch Manager
- Hozz létre egy External vagy Internal switch-et

---

**Szerző**: Magyar Gyula
**Dátum**: 2025-02-04
**Cél**: Portfólió projekt - Hyper-V és Homelab dokumentáció
