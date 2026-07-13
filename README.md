# KROMSTR

**Kromoestereopsia sortzailea · edozein irudi + sakonera-mapa**

Nabigatzailean bertan lan egiten duen tresna bakarreko (single-file) aplikazioa, edozein irudi **kromoestereopsia** efektu batean bihurtzeko: koloreak berak sortzen du sakonera-sentsazioa, betaurreko 3Drik gabe.

---

## Zer den

Kromoestereopsia gure begiaren **aberrazio kromatiko** naturalean oinarritzen da: uhin-luzera luzeek (gorria) eta laburrek (urdina) begi-lentean apur bat desberdin fokatzen dute, eta horrek disparitate txiki bat sortzen du kolore bakoitzarentzat. Garunak disparitate hori **sakonera** gisa interpretatzen du.

Ondorioz, hondo ilun baten gainean:

- **Gorri saturatua → aurrera** dator
- **Urdin saturatua → atzera** joaten da

KROMSTR-ek irudi baten sakonera-informazioa hartu (sakonera-mapa batetik edo argitasunetik) eta gorri/urdin mapaketa horretara itzultzen du, WebGL2 shader batekin, denbora errealean.

> ⚠️ Jendearen **heren batek gutxi gorabehera alderantziz** hautematen du efektua (gorria atzera). Hori ez da akatsa, fisiologia baizik: horretarako dago *Alderantzikatu estereoa* aukera.

---

## Ezaugarriak

- **Irudia + sakonera-mapa** iturri gisa (edo argitasuna, maparik ez badago).
- **Sakonera automatikoa** [Depth Anything V2] modeloarekin, dena nabigatzailean (ikus behean).
- WebGL2 shader bat, pasada bakarrean: gorri/urdin konposizioa, kanal berdea 0an, jatorrizko irudiaren testura mantenduz.
- Bi alderantzikatze independente: sakonera-norabidea eta gorri/urdin estereoa.
- Kontrol finak: erdiko puntua, zabalera, saturazioa, argitasunaren eragina, gamma eta beltz-maila.
- **Paralaxe** aukerakoa: kanalen desplazamendu fisikoa sakoneraren arabera, efektua indartzeko.
- **Sakonera ikuspegia**, mapa gris eskalan ikusteko koloreztatu aurretik.
- **PNG esportazioa** jatorrizko bereizmenean.
- Hiru hizkuntzatako interfazea: **euskara / gaztelania / ingelesa**.
- Framework gabe, mendekotasun gabe (letra-tipoak izan ezik): HTML fitxategi bakarra.

---

## Erabilera

1. Ireki `kromstr.html` nabigatzaile moderno batean.
2. **Kargatu irudi bat** (arrastatu edo egin klik "Irudia" kutxan).
3. Sakonera lortzeko, bi bide:
   - **Kargatu sakonera-mapa** eskuz ("Sakonera-mapa" kutxan), edo
   - Sakatu **Sortu automatikoki** (Depth Anything V2).
4. Doitu kontrolak nahi den efektua lortu arte.
5. Sakatu **Esportatu PNG**.

Sakonera-maparik gabe, tresnak irudiaren **argitasuna** erabiliko du sakonera hurbilpen gisa (aukeratu *Argitasuna* iturri gisa).

---

## Sakonera automatikoa (Depth Anything V2)

**Sortu automatikoki** botoiak [Depth Anything V2] modeloa erabiltzen du sakonera-mapa bat kalkulatzeko, **osorik nabigatzailean**, [Transformers.js] bidez. Ez da zerbitzaririk behar, eta zure irudia **ez da gailutik ateratzen**.

Gailuaren arabera automatikoki egokitzen da:

| Egoera | Erabiltzen dena |
| --- | --- |
| WebGPU + `shader-f16` euskarria | WebGPU · `fp16` (arinena eta azkarrena) |
| WebGPU `shader-f16`-rik gabe | WebGPU · `fp32` (GPUan, deskarga handiagoa) |
| WebGPUrik gabe | WASM / CPU · `q8` (~25 MB, non-nahi dabil) |

GPU-bidea huts egiten badu, automatikoki **WASM/CPU**ra jotzen du.

> **Oharra:** lehen aldian liburutegia (CDN) eta modeloa deskargatu behar dira. Ondoren nabigatzaileak katxean gordetzen ditu. `file://` bidez sarerik gabe irekiz gero, botoi hau ez da ibiliko; gainerako tresnak bai.

Depth Anything-en hitzarmena **hurbil = argi** da, eta bat dator shader-arekin (argi → gorri → aurrera). Erliebea alderantziz ateratzen bada, erabili **Alderantzikatu sakonera**.

---

## Kontrolak

| Kontrola | Zertarako |
| --- | --- |
| **Sakonera-iturria** | Sakonera-mapa ala argitasuna erabili |
| **Alderantzikatu sakonera** | Maparen hurbil/urrun hitzarmena iraultzen du |
| **Erdiko puntua** | Gorri↔urdin trantsizioa non kokatzen den |
| **Zabalera** | Trantsizioaren kontraste/zorroztasuna |
| **Alderantzikatu estereoa** | Gorria eta urdina trukatzen ditu (heren batek alderantziz ikusten du) |
| **Saturazioa** | Kolorearen indarra (1 = hutsa; jaitsi konparatzeko) |
| **Argitasunaren eragina** | Jatorrizko testura vs. kolore laua (0 = kolore laua) |
| **Gamma** | Argitasunaren kurba doitzen du |
| **Beltz-maila** | Hondoa iluntzen/garbitzen du |
| **Paralaxea** | Kanalen desplazamendu fisikoa (0 = kromoestereopsia hutsa) |
| **Ikuspegia** | Emaitza ala sakonera gris eskalan |

---

## Baldintza teknikoak

- **WebGL2** onartzen duen nabigatzaile modernoa (efektua bera).
- Sakonera automatikorako: internet-konexioa lehen aldian, eta ahal dela **WebGPU** (Chrome/Edge berrietan). WebGPUrik ezean, CPUan ibiliko da (motelago irudi handietan).
- Ez da instalaziorik behar; fitxategia bikoiztu klik eginez irekitzen da.

---

## Teknologia

- WebGL2 + GLSL (vanilla JavaScript, frameworkik gabe).
- [Transformers.js] + [Depth Anything V2] (ONNX Runtime Web) sakonera automatikorako.
- Letra-tipoak: **Bebas Neue** + **JetBrains Mono** (Google Fonts).
- HTML fitxategi bakarra, eramangarria.

---

## Pribatutasuna

Prozesu guztia **lokala** da: irudiak eta sakonera-kalkuluak zure nabigatzailean gertatzen dira. Ez da irudirik igotzen inolako zerbitzarira. Sarea liburutegia eta modeloa deskargatzeko baino ez da erabiltzen (behin).

---

## Kredituak

Sakonera-estimazioa: **Depth Anything V2** (`onnx-community/depth-anything-v2-small`), **Transformers.js** bidez exekutatua.

**B_RR_K_** · tresna sorta.

[Depth Anything V2]: https://github.com/DepthAnything/Depth-Anything-V2
[Transformers.js]: https://github.com/huggingface/transformers.js
