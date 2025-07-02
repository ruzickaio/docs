Zde je podrobný příklad nastavení **WebGrab+Plus (WG++) s EPGImporterem** pro české TV na Enigma2 zařízení (např. set-top boxy s OpenATV, OpenPLi):

---

### **Krok 1: Instalace WebGrab+Plus na PC/NAS (zdroj EPG dat)**
1. **Stáhněte WG++** z oficiálního webu: [www.webgrabplus.com](https://www.webgrabplus.com)  
   → Vyberte verzi pro váš OS (Windows/Linux/Mac).

2. **Nakonfigurujte WG++**:
   - Vytvořte složku `WG++` (např. `C:\WG++`).
   - Rozbalte stažený soubor a zkopírujte do ní obsah.
   - Upravte konfigurační soubor `WebGrab++.config.xml`:
     ```xml
     <settings>
       <filename>epg.xml</filename>   <!-- Výstupní název EPG souboru -->
       <mode>g</mode>                 <!-- Režim generování -->
       <postprocess grab="y" run="n"/> <!-- Žádné postprocesing -->
     </settings>
     ```
   - Upravte `siteini.pack` (zdroje EPG):
     - Stáhněte české konfigurace z [monster99 repository](https://github.com/monster99/all_webgrabplus_siteini) nebo [GitHub: siteini](https://github.com/DeBaschdi/webgrabplus-siteinipack):
       ```bash
       # Příklad pro Linux/macOS:
       git clone https://github.com/monster99/all_webgrabplus_siteini
       cp -r all_webgrabplus_siteini/cz/* WG++/siteini.user/
       ```

3. **Upravte `channel_list.xml`** (mapování kanálů):
   ```xml
   <channel site="ct1.ceskatelevize.cz" site_id="ct1" update="i">ČT1 HD</channel>
   <channel site="nova.nova.cz" site_id="nova" update="i">NOVA HD</channel>
   <channel site="prima.iprima.cz" site_id="prima" update="i">PRIMA HD</channel>
   ```
   - `site_id`: ID z `siteini` složky (najdete v `.ini` souborech, např. `cz/ct1.ceskatelevize.cz.ini`).
   - `<channel>...</channel>`: **Přesný název kanálu z vašeho IPTV playlistu** (např. z `playlist.m3u`).

4. **Spusťte WG++**:
   ```bash
   # Windows:
   WebGrab+Plus.exe
   
   # Linux/macOS:
   mono WebGrab+Plus.exe
   ```
   - Výstup: Soubor `epg.xml` s českým EPG.

---

### **Krok 2: Sdílení EPG souboru do sítě**
1. **Umístěte `epg.xml` na síťové úložiště** (např. SMB/NFS):
   - Příklad cesty: `\\NAS\share\epg.xml` nebo `/mnt/nas/epg.xml`.

---

### **Krok 3: Nastavení EPGImporteru v Enigma2**
1. **Přidejte EPG zdroj**:
   - Otevřete **EPGImporter** (v menu Enigma2).
   - Jděte do **Sources** → **Blue (Add)**.
   - Zadejte cestu k `epg.xml`:
     ```
     file:///mnt/nas/epg.xml   # Pro lokální/NFS úložiště
     ```
     nebo
     ```
     http://IP_PC_NAS:PORT/epg.xml  # Pokud soubor hostujete přes HTTP (např. pomocí Python serveru)
     ```

2. **Aktivujte zdroj**:
   - Zaškrtněte váš zdroj v **Sources**.
   - Nastavte **"Automatic import"** (např. každý den ve 4:00).

3. **Ruční import**:
   - Stiskněte **Yellow (Manual import)** pro okamžité načtení EPG.

---

### **Krok 4: Kontrola a řešení chyb**
- **Problém**: EPG se nenačítá.
  - Řešení: V logu Enigma2 (`/var/log/`) zkontrolujte chyby. Typické příčiny:
    - Špatná cesta k `epg.xml` → ověřte přístupová práva.
    - Nesoulad názvů kanálů v `channel_list.xml` a playlistu.
    - Neaktuální `siteini` soubory → aktualizujte z GitHubu.

- **Problém**: EPG jen pro některé kanály.
  - Řešení: Upravte `channel_list.xml` tak, aby názvy kanálů **odpovídaly přesně IPTV playlistu** (včetně diakritiky a přípon jako "HD").
    ```xml
    <!-- Špatně -->
    <channel site="ct1.ceskatelevize.cz" site_id="ct1">ČT1</channel>
    
    <!-- Správně (pokud playlist má "ČT1 HD") -->
    <channel site="ct1.ceskatelevize.cz" site_id="ct1">ČT1 HD</channel>
    ```

---

### **Doporučené siteini pro ČR**
| **Kanál**       | **Site ID**               | **Konfigurační soubor**              |
|-----------------|---------------------------|--------------------------------------|
| ČT1             | `ct1`                     | `ct1.ceskatelevize.cz.ini`           |
| Nova            | `nova`                    | `nova.nova.cz.ini`                   |
| Prima           | `prima`                   | `prima.iprima.cz.ini`                |
| Barrandov TV    | `barrandovtv.barrandov.cz`| `barrandovtv.barrandov.cz.ini`       |

---

### **Upozornění**
- WebGrab+Plus **vyžaduje pravidelnou aktualizaci `siteini`** (zdroje se často mění).
- Pro automatizaci: Nastavte **cron úlohu** na PC/NAS pro denní spuštění WG++.
- Pokud nechcete hostovat EPG sami, použijte veřejný zdroj z `TVprogram.cz`:
  ```
  http://www.telka.cz/xmltv.php?user=demo&password=demo
  ```
  (v EPGImporteru přidejte jako zdroj).

Toto nastavení zajistí kompletní EPG pro české kanály bez poplatků. Pro rychlejší řešení lze použít **Jedi Maker Xtream**, který generuje EPG automaticky z IPTV providera.