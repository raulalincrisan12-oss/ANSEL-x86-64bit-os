# Ansel OS

Ansel OS este un sistem de operare x86-64 bare-metal construit de la zero, fără
Linux sau alt kernel dedesubt. Ținta fizică inițială este HP ProBook 6550b cu
Intel Core i5-430M și Intel HD Graphics; QEMU rămâne mediul sigur pentru build și
testare.

## Starea curentă: M0.19 DVD-ROM AHCI/ATAPI

Sistemul pornește prin BIOS și UEFI și oferă acum:

- kernel ELF x86-64 freestanding, scris în C;
- boot Limine de pe ISO și imagine HDD/USB;
- splash animat și bootlogo ANSEL fără elemente Android;
- framebuffer Limine cu backbuffer software și prezentare pe dreptunghiuri;
- compositor cu snapshot pentru mutarea fluidă a ferestrelor;
- tastatură PS/2 cu caractere ASCII, Shift/Caps Lock și taste de editare;
- touchpad/mouse PS/2 cu cursor software;
- wizard de pornire inspirat de Windows 11, cu `Încearcă Ansel` și
  `Instalează Ansel`;
- desktop modern, wallpaper, taskbar centrat, meniu Start și tray cu oră,
  dată și an;
- ferestre cu drag, redimensionare din margini și colțuri, minimizare,
  maximizare/restaurare și închidere;
- File Explorer interactiv cu sidebar, istoric, navigare și preview text;
- meniuri de clic dreapta Windows 11-style pe desktop, foldere, fișiere și
  unități, cu Open/Open with, Refresh, Properties, Eject și Format;
- VFS în RAM cu foldere, documente, imagine BMP și `Notes.txt` editabil;
- Terminal/CMD cu prompt și comenzi interne;
- Notepad cu editare și salvare în VFS-ul din RAM;
- Code Box cu editor, canvas 2D live și interpreterul sigur AnselScript pentru
  animații și jocuri controlate de la tastatura laptopului;
- driver Intel High Definition Audio cu detectare PCI/MMIO, enumerare dinamică
  a codec-ului, rutare analogică, EAPD/amplificatoare și ieșire PCM stereo prin
  DMA/BDL; suportă tonuri și streaming audio continuu cu volum software;
- Photos/editor pentru BMP 24/32-bit, cu zoom, rotire, luminozitate și alb-negru;
- Media Player pentru MP3 redat direct de pe VFS/USB, cu play/pause, restart,
  mute și volum 0–100%, plus AVI cu RGB 4/8/16/24/32-bit, bitfields, RLE4/RLE8,
  YUY2/UYVY, I420/IYUV/YV12/NV12 și MJPEG baseline;
- Ansel Raider, joc raycaster 3D original, cu hartă, inamici, armă, viață,
  muniție, scor și obiectiv de extracție, randat integral fixed-point;
- AnselCraft 2, joc voxel original cu lume procedurală extinsă la cerere,
  cinci biomuri, minereuri, copaci, apă, zăpadă, texturi locale și ceață;
- meniuri Play/Create World/Settings, moduri Creative și Survival, dificultăți
  Peaceful/Easy/Normal/Hard, inventar 36-slot, crafting 3×3 și armură;
- unelte pe niveluri, hrană, viață/foame, vaci, porci, oi, zombi și schelete,
  plus control WASD cu apăsare continuă și mouse/trackpad-look capturat;
- ISO Explorer read-only pentru imagini ISO9660;
- driver SATA/AHCI ATAPI pentru unități DVD, cu IDENTIFY PACKET, INQUIRY,
  REQUEST SENSE, READ CAPACITY și READ(12), sectoare optice de 2048 bytes;
- disc ISO9660 montat read-only direct în VFS și afișat separat în sidebar-ul
  Explorerului, inclusiv când este montat simultan și un stick USB;
- Settings separat, cu pagini System, Display, Sound, Devices,
  Personalization și About;
- font proporțional anti-aliased DejaVu Sans, în locul fontului pixelat;
- citirea ceasului CMOS/RTC și log de diagnostic prin serial.
- enumerare PCI legacy și mapare MMIO pentru BAR-urile care nu sunt incluse în
  HHDM;
- allocator DMA sub 4 GiB și drivere EHCI/UHCI USB 2.0 în polling;
- manager USB dinamic cu adrese multiple, conectare/deconectare și registry de
  dispozitive;
- tastaturi și mouse-uri USB HID boot-protocol, inclusiv input real în shell;
- hub-uri USB externe, port power/reset și dispozitive conectate în aval;
- notificări Windows 11-style pentru conectare, deconectare, erori, eject și
  evenimente de sistem;
- enumerare USB high/full/low-speed, Bulk-Only Transport și comenzi SCSI reale;
- block device, superfloppy/MBR/partiții extinse/GPT și FAT12/FAT16/FAT32;
- exFAT read-only, inclusiv fișiere contigue și lanțuri de clustere;
- volum USB montat în VFS, afișat în sidebar-ul și directoarele Explorerului;
- până la 256 de intrări USB publicate recursiv și citire streaming pentru
  fișiere care nu încap în cache;
- citire de documente de pe stick și suprascriere sigură a fișierelor existente,
  urmată de `SYNCHRONIZE CACHE` și demontare.
- formatare FAT32 reală a întregului stick, numai după confirmare distructivă.

## Ce este real și ce urmează

Pagina de instalare nu scrie încă pe SSD/HDD. Ea verifică starea stocării și
blochează în mod sigur instalarea până când kernelul are calea ATA pentru HDD/SSD,
partiții GPT/MBR și FAT cu scriere testată. Driverul AHCI existent în acest
milestone deservește numai unitatea optică ATAPI read-only. Butonul disponibil pornește
sesiunea live; nu simulează o instalare reușită.

Explorer combină VFS-ul propriu din RAM cu volumele USB și DVD atunci când sunt
montate. DVD-ul apare ca ISO9660 read-only; comanda Terminal `dvd` arată unitatea,
eticheta și capacitatea. Notepad salvează `Notes.txt` în RAM și poate suprascrie în siguranță
un document text USB deja alocat. Photos, Media Player și ISO Explorer citesc
fișierele direct prin VFS streaming. Terminalul execută comenzi interne ale
kernelului, nu procese user-mode.

Un stick USB poate fi conectat înainte sau după boot și este montat automat dacă
folosește FAT12/16/32 ori exFAT, direct pe disc sau într-o partiție MBR/GPT.
Funcționează atât traseul EHCI high-speed, cât și UHCI full-speed. Explorer arată
recursiv directoarele și fișierele. Terminalul poate suprascrie conținutul unui
fișier FAT existent de cel mult 64 KiB și poate face flush/eject logic. exFAT
rămâne read-only. Scoaterea fizică demontează imediat volumul din VFS și produce
o avertizare dacă nu s-a folosit eject.

Clasele funcționale sunt HID boot keyboard/mouse, hub și Mass Storage BOT/SCSI.
În mod intenționat, această versiune nu creează, șterge sau redimensionează
fișiere individuale și nu montează NTFS. Formatarea FAT32 șterge întregul
dispozitiv și cere confirmarea explicită `ERASE`. xHCI/USB 3.x, audio USB,
webcam, imprimantă și HID non-boot cer drivere de clasă suplimentare; un
dispozitiv necunoscut este enumerat și raportat, dar nu este prezentat ca
funcțional.
Detaliile sunt în [docs/USB-STORAGE.md](docs/USB-STORAGE.md) și
[docs/DVD-ROM.md](docs/DVD-ROM.md).

Backbufferul elimină pâlpâirea produsă de desenarea directă a fiecărei etape.
Mutarea folosește o captură a ferestrei și actualizează numai zona veche/nouă,
iar editarea nu mai recopiază snapshotul la fiecare tastă. Fără un driver Intel
Ironlake KMS nu există încă vsync, page-flip, accelerare sau schimbare de mod, așa
că pe hardware poate rămâne tearing în mișcare.

Ora și data provin direct din CMOS. Fusul orar și sincronizarea prin rețea nu
sunt încă implementate. Intel HDA redă PCM S16 stereo la 48 kHz și este verificat
end-to-end în QEMU cu un codec HDA și backend WAV. Streamingul folosește opt
descriptori BDL și recuperează toate segmentele consumate după o redesenare lentă,
fără repetarea PCM-ului în repaus. Traseul generic include power, EAPD și unmute
pentru codec-uri de laptop; codec-ul IDT al HP-ului fizic mai cere validare pe
aparat și, dacă firmware-ul o impune, fixup-uri GPIO/vendor specifice plăcii de
bază.

Decodoarele media folosesc buffere fixe și refuză intrările trunchiate sau
malformate. MP3-ul este decodat incremental și convertit la PCM S16 stereo
48 kHz pentru HDA; playerul AVI indexează cel mult 512 cadre per fișier.
H.264/H.265, VP9, AV1, AAC și containerele MP4/MKV/MOV nu sunt încă implementate;
extensiile respective afișează explicit starea nesuportată.

AnselCraft 2 este un joc bare-metal original. Lumea nu mai este limitată la un
singur chunk: terenul, biomurile și resursele sunt generate determinist la orice
coordonată vizitată, iar modificările jucătorului sunt păstrate în sesiunea
curentă. Include Creative/Survival, cele patru dificultăți, inventar, crafting,
minereuri, unelte, armură, hrană, animale și monștri. Nu rulează Java Edition,
nu încarcă lumi sau asset-uri Minecraft și nu are încă salvare persistentă pe
disc, multiplayer, Redstone ori dimensiuni alternative; `Save and Quit` păstrează
lumea în RAM până la oprirea sistemului.

## Code Box și fișiere `.ans`

Code Box rulează AnselScript, un limbaj mic și determinist integrat în kernel.
Un script poate păstra variabile, poate reacționa la taste apăsate sau ținute și
poate desena pixeli, linii, dreptunghiuri, cercuri, text și numere pe un canvas
de 320×180 la 30 FPS. Exemplul `Documents/Moving Square.ans` și butonul `Demo`
oferă un punct de plecare care poate fi modificat direct. Comenzile `tone` și
`silence` folosesc driverul HDA fără a expune scriptului registre sau memorie DMA.

Fluxul de lucru de pe Windows este:

1. scrie codul în Notepad și salvează-l ca `nume.ans`, alegând UTF-8 sau ANSI și
   `Save as type: All files`;
2. copiază fișierul pe un stick FAT12/16/32 ori exFAT;
3. deschide stickul în File Explorer din Ansel OS și dă dublu-click pe fișier;
4. apasă `Run`; erorile indică linia exactă. Pe FAT, `Save` suprascrie fișierul
   USB existent dacă noul text încape în dimensiunea lui inițială, iar exFAT
   rămâne read-only.

Scripturile sunt izolate: nu execută `.exe`, machine code sau comenzi de kernel
și nu accesează memoria ori fișierele arbitrar. Există limite explicite de
16 KiB sursă, 384 instrucțiuni, 32 de variabile și 4096 de instrucțiuni per
eveniment, astfel încât o buclă infinită este oprită în loc să blocheze sistemul.
Sintaxa și toate comenzile sunt documentate în
[docs/ANSELSCRIPT.md](docs/ANSELSCRIPT.md).

## Control

În wizardul de pornire:

- săgeți stânga/dreapta: aleg Live sau Install și schimbă opțiunile;
- `Tab` ori săgeți sus/jos: schimbă controlul focalizat;
- `Enter` sau `Space`: confirmă controlul selectat;
- `R` și `E`: aleg rapid limba română sau engleză pe pagina de limbă;
- click stânga: selectează și activează controalele.

În shell:

- tasta Windows: deschide/închide Start;
- `E`: deschide File Explorer;
- `R`: deschide Settings;
- `G`: deschide rapid Ansel Raider;
- `C`: deschide rapid AnselCraft;
- `B`: deschide rapid Code Box;
- săgeți, `Enter`, `Backspace` și `Esc`: navigare în Explorer/Settings;
- taskbar/Start: deschid Terminal, Notepad, Photos, Explorer și Settings;
- drag pe bara de titlu: mută fereastra;
- drag pe margini sau colțuri: redimensionează fereastra;
- butoanele din dreapta barei: minimize, maximize/restore și close;
- clic dreapta: meniu contextual pentru desktop, fișier, folder sau unitate;
- Terminal: `help`, `clear`, `ver`, `date`, `time`, `mem`, `ls`, `cat`,
  `echo`, `usb`, `usbwrite <fișier-existent> <text>`, `usbeject` și comanda
  distructivă exactă `usbformat ERASE`;
- Notepad: caractere ASCII, Enter, Tab, Backspace, Delete, săgeți,
  Home și End.

În Ansel Raider:

- `W`/`S` sau săgeți sus/jos: înainte/înapoi;
- `A`/`D`: deplasare laterală, iar săgețile stânga/dreapta rotesc camera;
- `Space` sau `F`: trage;
- `E`: activează ieșirea după eliminarea tuturor inamicilor;
- `R`: reîncepe misiunea; `Esc`: închide jocul.

În AnselCraft:

- click în viewport: capturează trackpad-ul/mouse-ul pentru privire liberă;
- mișcarea trackpad-ului/mouse-ului: rotește și înclină camera; primul `Esc`
  eliberează pointerul, iar al doilea deschide pauza;
- `W`/`A`/`S`/`D`: deplasare continuă; privirea se controlează exclusiv din
  trackpad/mouse, nu din săgeți;
- `Space`: săritură cu gravitație și coliziuni;
- `F` sau clic stânga: sparge blocul de la crosshair;
- clic dreapta: plasează blocul selectat sau consumă hrana din mână;
- `E`: deschide/închide inventarul și crafting-ul 3×3;
- `1`–`9`: selectează slotul din hotbar;
- în inventar, click stânga mută/combină stivele, click dreapta le împarte și
  echipează armura; `R` regenerează lumea curentă.

În Code Box:

- `Run/Stop`: compilează și pornește sau oprește scriptul;
- `Demo`: încarcă exemplul inclus; `Reload`: recitește fișierul deschis;
- în editor: caractere ASCII, Enter, Tab, Backspace, Delete, săgeți, Home și End;
- în timpul rulării, toate tastele declarate de script ajung la evenimentele
  `keydown`/`keyup`, iar `ifkey` detectează apăsarea continuă;
- `Esc`: oprește mai întâi scriptul; apăsat din nou închide fereastra.

În Media Player pentru MP3:

- `Space`: play/pause; `R`, săgeată stânga sau `Home`: restart;
- săgeți sus/jos ori `+`/`-`: volum cu ±5%; `M`: mute/unmute;
- butoanele ferestrei oferă Restart, Play/Pause, Stop, Vol - și Vol +.

## Build pe Windows prin WSL

În distribuția WSL sunt necesare `build-essential`, `git`, `curl`, `xorriso`,
`mtools`, `gdisk`, `qemu-system-x86`, `ovmf`, `netpbm`, ImageMagick și Pillow
pentru regenerarea atlasului de font.

```bash
sudo apt install build-essential git curl xorriso mtools dosfstools gdisk qemu-system-x86 ovmf netpbm imagemagick python3-pil
```

Din PowerShell:

```powershell
.\tools\build.cmd
.\tools\build.cmd -Target all-hdd
```

Rezultatele sunt `ansel-os.iso` și `ansel-os.hdd`.

Teste automate principale:

```powershell
wsl bash tools/test-interactive-qemu.sh
wsl bash tools/capture-installer-qemu.sh
wsl bash tools/test-window-controls-qemu.sh
wsl bash tools/test-apps-qemu.sh
wsl bash tools/test-resize-qemu.sh
wsl bash tools/test-usb-storage-qemu.sh
wsl bash tools/test-usb-uhci-storage-qemu.sh
wsl bash tools/test-usb-hid-qemu.sh
wsl bash tools/test-usb-hotplug-qemu.sh
wsl bash tools/test-usb-write-qemu.sh
wsl bash tools/test-usb-exfat-qemu.sh
wsl bash tools/test-usb-gpt-fat-qemu.sh
wsl bash tools/test-usb-format-qemu.sh
wsl bash tools/test-context-menu-qemu.sh
wsl bash tools/test-format-dialog-qemu.sh
wsl bash tools/test-file-utilities-qemu.sh
wsl bash tools/test-video-codecs.sh
wsl bash tools/test-media-codecs-qemu.sh
wsl bash tools/test-raycaster.sh
wsl bash tools/test-raycaster-qemu.sh
wsl bash tools/test-ansel-script.sh
wsl bash tools/test-code-box-qemu.sh
wsl bash tools/test-hda-qemu.sh
wsl bash tools/test-mp3.sh
wsl bash tools/test-mp3-qemu.sh
wsl bash tools/test-iso9660.sh
wsl bash tools/test-dvd-qemu.sh
wsl bash tools/test-voxel.sh
wsl bash tools/test-voxel-qemu.sh
wsl bash tools/test-boot-matrix.sh
```

Capturile și logurile seriale sunt salvate în `out/`.

## Structură

```text
assets/                     bootlogo, animație, wallpaper și licența fontului
kernel/src/drivers/         serial, PIT, PS/2, RTC, PCI, AHCI/ATAPI, HDA, EHCI/UHCI
kernel/src/usb/             descriptori, manager hot-plug, hub și USB HID
kernel/src/storage/         block device, DVD și USB Mass Storage BOT/SCSI
kernel/src/fs/              VFS, ISO9660, MBR/GPT, FAT12/16/32 și exFAT
kernel/src/game/            motoarele fixed-point Ansel Raider și AnselCraft
kernel/src/graphics/        framebuffer, backbuffer, BMP și font anti-aliased
kernel/src/media/           decodoare bounded MP3, RGB, RLE, YUV și JPEG baseline
kernel/src/script/          compilatorul și runtime-ul sandboxat AnselScript
kernel/src/ui/              setup, shell, ferestre, Explorer și aplicații
tools/                      build, generator font, capturi și teste QEMU
```

Nu există intenționat un script automat de scriere pe USB: selectarea greșită a
discului poate distruge date. Imaginea HDD se testează întâi în QEMU, apoi
dispozitivul fizic trebuie identificat explicit.

Vezi [roadmap-ul](docs/ROADMAP.md) și
[strategia HP ProBook 6550b](docs/HP-PROBOOK-6550B.md).

## Proveniență

Structura de boot urmează șablonul oficial `limine-c-template-x86-64`, fixat la
commitul documentat de `kernel/get-deps`. Bootloaderul este Limine v12.5.2.
Wallpaperul Ansel este un asset original al proiectului. Atlasul folosește
DejaVu Sans; licența este inclusă în `assets/fonts/DEJAVU-LICENSE.txt`.
Ansel Raider folosește doar hartă și grafică procedurală originale; nu include
cod, WAD-uri, sunete sau alte asset-uri din Doom.
AnselCraft nu include cod, texturi, sunete sau alte asset-uri Minecraft; lumea,
materialele și randarea voxel sunt implementate original în proiect.
Decoderul MP3 include minimp3 la commitul documentat în
`tools/fixtures/README.md`; codul și vectorul de test folosit sunt distribuite
sub CC0, iar copia licenței este în `kernel/src/media/third_party/minimp3.LICENSE`.
