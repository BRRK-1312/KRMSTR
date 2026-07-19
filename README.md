# KROMSTR

**Kromoestereopsia sortzailea · irudia eta bideoa · sakonera-mapa + dither**

Nabigatzailean bertan lan egiten duen tresna bakarreko (single-file) aplikazioa, edozein **irudi edo `.mp4` bideo** kromoestereopsia efektu batean bihurtzeko: koloreak berak sortzen du sakonera-sentsazioa, betaurreko 3Drik gabe. Dither Bayer, tonu-doikuntzak eta fusio-moduekin osatua, eta MP4 esportazioarekin.

---

## Zer den

Kromoestereopsia gure begiaren **aberrazio kromatiko** naturalean oinarritzen da: uhin-luzera luzeek (gorria) eta laburrek (urdina) begi-lentean apur bat desberdin fokatzen dute, eta horrek disparitate txiki bat sortzen du kolore bakoitzarentzat. Garunak disparitate hori **sakonera** gisa interpretatzen du. Hondo ilun baten gainean, **gorri saturatua aurrera** dator eta **urdin saturatua atzera** joaten da.

KROMSTR-ek irudiaren (edo bideoaren) sakonera-informazioa hartu —sakonera-mapa batetik edo argitasunetik— eta gorri/urdin mapaketa horretara itzultzen du, WebGL2 shader batekin, denbora errealean.

> ⚠️ Jendearen **heren batek** gutxi gorabehera **alderantziz** hautematen du efektua (gorria atzera). Ez da akatsa, fisiologia baizik: horretarako dago *Alderantzikatu estereoa*.

---

## Ezaugarriak

- **Irudia + sakonera-mapa**, edo **bideoa (.mp4) + sakonera-bideoa**, iturri gisa (bestela argitasuna).
- Bideoen **denbora errealeko** aurrebista; kolore- eta sakonera-bideoak sinkronizatuta.
- **Sakonera automatikoa** irudietarako, Depth Anything V2 modeloarekin, dena nabigatzailean.
- WebGL2 pipeline osoa: efektu kromatikoa → **tonu-doikuntza** → lausoa → **dither Bayer** → **fusio-modua**.
- Dither Bayer 2×2 / 4×4 / 8×8, atari-doikuntzarekin, alderantzikatzearekin eta **beltz/zurien babesarekin** (hondoak garbi mantentzeko).
- **Fusio-moduak** (blend): Normal, Multiply, Screen, Overlay, Dodge, Burn, Difference.
- Tonu-doikuntzak: argitasuna, kontrastea, saturazioa, lausoa, puntu beltza/zuria, posterizazioa.
- **PNG esportazioa** (irudia) eta **MP4 esportazioa** (bideoa) ffmpeg.wasm bidez, WebM babesarekin.
- Hiru hizkuntzatako interfazea: **euskara / gaztelania / ingelesa**.
- Framework gabe, HTML fitxategi bakarra.

---

## Erabilera — irudia

1. Ireki `kromstr.html` nabigatzaile moderno batean.
2. **Kargatu irudi bat** ("Irudia" kutxan).
3. Sakonera lortzeko: **kargatu sakonera-mapa** eskuz, edo sakatu **Sortu automatikoki** (Depth Anything V2).
4. Doitu kontrolak.
5. Sakatu **Esportatu PNG**.

Sakonera-maparik gabe, tresnak irudiaren **argitasuna** erabiliko du sakonera hurbilpen gisa.

## Erabilera — bideoa

1. Sortu **sakonera-bideoa ComfyUI-n** (adib. Depth Anything 3 / DA3-Streaming) zure kolore-bideotik.
2. KROMSTR-en, **kargatu kolore-bideoa** "Irudia" kutxan eta **sakonera-bideoa** "Sakonera-mapa" kutxan (biak `.mp4`).
3. Biak sinkronizatuta erreproduzitzen dira; efektua zuzenean aplikatzen da.
4. Ezarri **FPS** (jatorrizko bideoaren berdina) eta sakatu **Esportatu MP4**.

> **Oharra:** sakonera automatikoa (Depth Anything nabigatzailean) irudietarako da soilik. Bideoetan ez da denbora errealekoa; horregatik sakonera-bideoa ComfyUI-tik inportatzen da.

---

## Sakonera automatikoa (Depth Anything V2)

**Sortu automatikoki** botoiak Depth Anything V2 erabiltzen du sakonera-mapa bat kalkulatzeko, **osorik nabigatzailean** (Transformers.js / ONNX). Ez da zerbitzaririk behar, eta irudia **ez da gailutik ateratzen**. Gailuaren arabera egokitzen da:

| Egoera | Erabiltzen dena |
| --- | --- |
| WebGPU + `shader-f16` euskarria | WebGPU · `fp16` (arinena) |
| WebGPU `shader-f16` gabe | WebGPU · `fp32` (GPUan) |
| WebGPU gabe | WASM / CPU · `q8` (~25 MB) |

GPU-bidea huts eginez gero, automatikoki **WASM/CPU**ra jotzen du. Lehen aldian liburutegia eta modeloa deskargatu behar dira (gero katxean).

Depth Anything V2 eta V3-ren hitzarmena **hurbil = argi** da, eta bat dator shader-arekin. Erliebea alderantziz aterata, erabili **Alderantzikatu sakonera**.

---

## Kontrolak

**Sakonera:** iturria (mapa/argitasuna), alderantzikatu sakonera, erdiko puntua, zabalera.

**Kolorea:** alderantzikatu estereoa (gorri↔urdin), saturazioa, argitasunaren eragina, gamma, beltz-maila.

**Irudia (tonu-doikuntza, ditherra baino lehen):** argitasuna, kontrastea, saturazioa (100 = neutroa), lausoa (px), puntu beltza/zuria (0–255), posterizatu.

**Dither · Bayer:** pixel-tamaina, matrizea (2×2/4×4/8×8), ataria (atalasearen desplazamendua), alderantzikatu, **babestu beltzak** eta **babestu zuriak** (dither-a mutur ilun/argietan itzaltzen du, hondoa garbi mantenduz).

**Fusioa (blend):** fusio-modua (Normal…Difference) + kopurua. Dither-a irudiaren gainean nola nahasten den definitzen du. Kopurua 0 = efektu garbia.

**Ikuspegia:** emaitza edo sakonera gris eskalan.

---

## Esportazioa

- **Irudia → PNG** jatorrizko bereizmenean.
- **Bideoa → MP4:** canvas-a MediaRecorder-ekin grabatzen da (WebM) eta gero **ffmpeg.wasm**-ekin MP4-ra (H.264) bihurtzen. Egoera-barrak *Grabatzen → ffmpeg kargatzen → MP4 kodetzen → Eginda* erakusten du. ffmpeg-ek huts eginez gero, **WebM** ematen du babes gisa.

Kontuan izan:

- ffmpeg.wasm **hari bakarrekoa** da: 30 s-ko 1080p klip batek minutu batzuk behar ditzake kodetzeko, eta lehen aldian core-a (~30 MB) deskargatzen du.
- **FPS** eskuz ezartzen da (nabigatzaileak ez du bideoaren benetako abiadura ematen); jarri jatorrizkoaren berdina iraupena zuzena izateko.
- Grabazioak bideoaren **pasaldi bat** hartzen du.

---

## Baldintza teknikoak

- **WebGL2** onartzen duen nabigatzaile modernoa.
- Sakonera automatikoa / MP4 esportazioa: internet-konexioa lehen aldian (liburutegiak eta modeloak deskargatzeko). Ahal dela **WebGPU** (Chrome/Edge) azkarragoa da.
- Instalaziorik gabe; fitxategia zuzenean irekitzen da. MP4 esportazioak `file://`-tik ere funtzionatzen du (worker-a blob gisa kargatzen da); arazorik izanez gero, erabili tokiko zerbitzari bat (`python -m http.server`).

---

## Pribatutasuna

Prozesu guztia **lokala** da: irudiak, bideoak eta kalkulu guztiak zure nabigatzailean gertatzen dira. Ez da ezer igotzen inolako zerbitzarira. Sarea liburutegiak eta modeloak deskargatzeko baino ez da erabiltzen (behin).

---

## Teknologia

- WebGL2 + GLSL (vanilla JavaScript, frameworkik gabe).
- [Transformers.js] + [Depth Anything V2] (ONNX Runtime Web) sakonera automatikorako.
- [ffmpeg.wasm] MP4 esportaziorako; MediaRecorder grabaziorako.
- Letra-tipoak: **Bebas Neue** + **JetBrains Mono**.
- HTML fitxategi bakarra, eramangarria.

---

## Kredituak

Sakonera-estimazioa: **Depth Anything V2** (`onnx-community/depth-anything-v2-small`), Transformers.js bidez. Bideorako sakonera: **Depth Anything 3** (ComfyUI-n sortua). MP4: **ffmpeg.wasm**.

**B_RR_K_** · tresna sorta.

[Depth Anything V2]: https://github.com/DepthAnything/Depth-Anything-V2
[Transformers.js]: https://github.com/huggingface/transformers.js
[ffmpeg.wasm]: https://github.com/ffmpegwasm/ffmpeg.wasm
