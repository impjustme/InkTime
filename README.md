# InkTime

InkTime este un smartwatch open-source cu consum redus, construit in jurul microcontroller-ului nRF52840, cu ecran e-paper, alimentare din baterie Li-Po, incarcare prin USB-C, monitorizare baterie, IMU si feedback haptic. Platforma a fost proiectata pentru autonomie buna, dimensiuni compacte si integrare mecanica intr-o carcasa dedicata.

## Diagrama bloc

```
                                +----------------------+
                                |       USB-C          |
                                |   alimentare / USB   |
                                +----------+-----------+
                                           |
                                           v
                                +----------------------+
                                |   ESD Protection     |
                                |    USBLC6-2SC6Y      |
                                +----------+-----------+
                                           |
                                           v
                                +----------------------+
                                |  Li-Po Charger       |
                                |    BQ25180YBGR       |
                                +----+------------+----+
                                     |            |
                                     |            +--------------------+
                                     |                                 |
                                     v                                 v
                           +-------------------+             +-------------------+
                           |    Li-Po Battery  |             |   Fuel Gauge      |
                           |     LP502030      |<-- I2C ---> |    MAX17048       |
                           +---------+---------+             +-------------------+
                                     |
                                     v
                           +-------------------+
                           |  Buck-Boost DC/DC |
                           |    RT6160AWSC     |
                           +---------+---------+
                                     |
                                   3V3/VREG
                                     |
        +----------------------------+------------------------------+
        |                            |                              |
        v                            v                              v
+---------------+          +-------------------+          +-------------------+
|   nRF52840    |<--I2C--->|      BMA423       |<--I2C--->|     DRV2605       |
| BLE MCU       |          |       IMU         |          |   Haptic Driver   |
+-------+-------+          +-------------------+          +---------+---------+
        |                                                              |
        | SPI + GPIO                                                    v
        v                                                      +----------------+
+-------------------+                                          | Vibration Motor |
| 1.54" E-Paper     |                                          |    10 x 2.7 mm  |
| via FPC connector |                                          +----------------+
+-------------------+
        |
        +--> boost / gate drive pentru EPD:
             MOSFET-uri + diode Schottky + inductoare + condensatori
```

## BoM

| Componenta       | Part number / Valoare  | Rol                           | Link JLC / sursa              | Datasheet                                                                                                                                                                              |
| ---------------- | ---------------------- | ----------------------------- | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MCU              | nRF52840               | microcontroller principal BLE | JLC Parts / external sourcing | [https://infocenter.nordicsemi.com/pdf/nRF52840_PS_v1.1.pdf](https://infocenter.nordicsemi.com/pdf/nRF52840_PS_v1.1.pdf)                                                               |
| PMIC charger     | BQ25180YBGR            | incarcare Li-Po prin USB-C    | JLC Parts                     | [https://www.ti.com/lit/ds/symlink/bq25180.pdf](https://www.ti.com/lit/ds/symlink/bq25180.pdf)                                                                                         |
| DC/DC            | RT6160AWSC             | conversie VBAT -> 3V3/VREG    | JLC Parts / external sourcing | [https://www.richtek.com/assets/product_file/RT6160A/DS6160A-05.pdf](https://www.richtek.com/assets/product_file/RT6160A/DS6160A-05.pdf)                                               |
| Fuel gauge       | MAX17048G+T10          | monitorizare baterie          | JLC Parts / external sourcing | [https://www.analog.com/media/en/technical-documentation/data-sheets/max17048-max17049.pdf](https://www.analog.com/media/en/technical-documentation/data-sheets/max17048-max17049.pdf) |
| IMU              | BMA423                 | accelerometru triaxial        | JLC Parts / external sourcing | [https://www.bosch-sensortec.com/products/motion-sensors/accelerometers/bma423/](https://www.bosch-sensortec.com/products/motion-sensors/accelerometers/bma423/)                       |
| Haptic driver    | DRV2605YZFR            | comanda motorului de vibratie | JLC Parts / external sourcing | [https://www.ti.com/lit/ds/symlink/drv2605.pdf](https://www.ti.com/lit/ds/symlink/drv2605.pdf)                                                                                         |
| ESD USB          | USBLC6-2SC6Y           | protectie ESD pentru USB      | JLC Parts                     | [https://www.st.com/resource/en/datasheet/usblc6-2.pdf](https://www.st.com/resource/en/datasheet/usblc6-2.pdf)                                                                         |
| Antena           | 2450AT18B100E          | antena 2.4 GHz                | external sourcing             | [https://www.johansontechnology.com/datasheets/antennas/2450AT18B100.pdf](https://www.johansontechnology.com/datasheets/antennas/2450AT18B100.pdf)                                     |
| Conector debug   | TC2030-IDC             | SWD / debugging               | external sourcing             | [https://www.tag-connect.com/product/tc2030-idc](https://www.tag-connect.com/product/tc2030-idc)                                                                                       |
| Conector display | 503480-2400            | conector FPC pentru e-paper   | external sourcing             | [https://www.molex.com/en-us/products/part-detail/5034802400](https://www.molex.com/en-us/products/part-detail/5034802400)                                                             |
| USB-C            | KH-TYPE-C-16P          | alimentare si interfata USB   | JLC Parts / external sourcing | link conform sursei alese                                                                                                                                                              |
| Cristal HF       | 32 MHz, SMD 2016       | ceas principal MCU            | JLC Parts / external sourcing | conform partului ales                                                                                                                                                                  |
| Cristal LF       | 32.768 kHz, SMD 3215   | RTC / low-power clock         | JLC Parts / external sourcing | conform partului ales                                                                                                                                                                  |
| MOSFET EPD       | SI1308EDL-T1-GE3       | comanda in etajul e-paper     | JLC Parts / external sourcing | [https://www.vishay.com/docs/68986/si1308edl.pdf](https://www.vishay.com/docs/68986/si1308edl.pdf)                                                                                     |
| MOSFET EPD       | DMG2305UX-7 / echiv.   | comanda in etajul e-paper     | JLC Parts / external sourcing | [https://www.diodes.com/assets/Datasheets/DMG2305UX.pdf](https://www.diodes.com/assets/Datasheets/DMG2305UX.pdf)                                                                       |
| Diode Schottky   | MBR0530                | etaj boost / redresare EPD    | JLC Parts / external sourcing | [https://www.onsemi.com/pdf/datasheet/mbr0530t1-d.pdf](https://www.onsemi.com/pdf/datasheet/mbr0530t1-d.pdf)                                                                           |
| Inductor power   | FTC252012SR47MBCA      | inductor pentru RT6160        | JLC Parts                     | [https://jlcpcb.com/partdetail/6763488-FTC252012SR47MBCA/C5832368](https://jlcpcb.com/partdetail/6763488-FTC252012SR47MBCA/C5832368)                                                   |
| Inductor EPD     | 68uH                   | inductor pentru boost EPD     | JLC Parts / external sourcing | conform partului ales                                                                                                                                                                  |
| Butoane          | EVP-AKE31A             | interfata utilizator          | JLC Parts / external sourcing | conform partului ales                                                                                                                                                                  |
| Baterie          | LP502030, 3.7V, 250mAh | sursa principala de energie   | external sourcing             | conform producatorului bateriei                                                                                                                                                        |
| Display          | 1.54" e-paper, 200x200 | afisaj principal              | external sourcing             | conform modelului ales                                                                                                                                                                 |
| Shaker           | 10 x 2.7 mm            | vibratie / feedback haptic    | external sourcing             | conform modelului ales                                                                                                                                                                 |
## Descriere hardware
1. Microcontroller principal

Sistemul este construit in jurul nRF52840, un microcontroller ARM Cortex-M4F cu BLE 5.x, suficient de performant pentru gestionarea afisajului e-paper, a senzorilor, a starii de baterie si a interfetei de utilizator.Pe partea de clock sunt folosite doua surse:

-un cristal de 32 MHz pentru clock-ul principal.

-un cristal de 32.768 kHz pentru RTC si scenarii low-power.

2. Alimentare si management energetic


Arhitectura de alimentare este impartita in trei blocuri.

BQ25180YBGR

Gestioneaza incarcarea bateriei Li-Po din USB-C. Intrarea de 5 V vine din VBUS, iar iesirea este conectata la bateria de o celula. Acest circuit este configurabil si monitorizabil, iar pinul de interrupt este dus catre MCU.

MAX17048

Monitorizeaza tensiunea si starea bateriei. Acesta comunica pe I2C cu microcontrollerul si expune un semnal de alerta pentru praguri critice sau schimbari de stare.

RT6160AWSC

Converteaza tensiunea bateriei intr-o alimentare stabila pentru sistemul digital. Acesta este blocul care alimenteaza rail-urile de lucru ale placii si perifericele sale.

In proiect, bateria nu este conectata printr-un conector JST clasic, ci direct la doua test pad-uri dedicate de pe PCB, una pentru VBAT si una pentru GND, pentru economie de spatiu.

3. Afisaj

Afisajul utilizat este un e-paper de 1.54", conectat printr-un conector FPC. Comunicatia cu MCU se face in principal prin SPI, iar pentru control sunt folosite si semnale GPIO dedicate:

CS

DC

RST

BUSY

Deoarece e-paper-ul necesita tensiuni si secvente specifice, exista un etaj discret dedicat pentru generarea si comutarea semnalelor de alimentare ale panelului, construit cu:

MOSFET-uri

diode Schottky

inductoare

condensatori de filtrare si bootstrap

4. IMU

BMA423 este accelerometrul triaxial folosit pentru detectie de miscare, pasi, gesturi si evenimente de trezire. Comunicatia se face pe I2C, iar semnalele de intrerupere sunt aduse pe GPIO-uri separate ale MCU-ului pentru reactii rapide si trezire din moduri low-power.

5. Feedback haptic

DRV2605 comanda un motor de vibratie compact. Comunicatia cu MCU se face pe I2C, iar activarea poate fi controlata si printr-un semnal GPIO dedicat. Acest bloc ofera feedback tactil pentru navigare, notificari si interactiuni de baza.

6. USB-C si protectie

Interfata USB-C este folosita pentru alimentare si, optional, interfata USB a microcontrollerului. Liniile sensibile sunt protejate cu USBLC6-2SC6Y. Liniile CC sunt terminate corespunzator pentru a permite functionarea corecta ca device.

7. RF si antena

Conectivitatea radio este realizata prin antena chip 2.4 GHz si o retea de matching RF. Antena este pozitionata la marginea PCB-ului, iar sub ea exista zona de keepout fara trasee si fara plan de masa, pentru a nu degrada performanta RF.

## Pini folositi

| Pin nRF52840  | Semnal         | Destinatie                                     | Motiv                             |
| ------------- | -------------- | ---------------------------------------------- | --------------------------------- |
| P0.00 / XL1   | XL1            | cristal 32.768 kHz                             | clock low-power / RTC             |
| P0.01 / XL2   | XL2            | cristal 32.768 kHz                             | clock low-power / RTC             |
| P0.04         | IMU_INT1       | BMA423                                         | intrerupere senzor                |
| P0.05         | IMU_INT2       | BMA423                                         | intrerupere senzor                |
| P0.06         | EPD_CS         | e-paper                                        | selectie SPI display              |
| P0.07         | EPD_DC         | e-paper                                        | selectie data/comanda             |
| P0.08         | EPD_RST        | e-paper                                        | reset display                     |
| P0.09         | PMIC_INT       | BQ25180                                        | interrupt charger / PMIC          |
| P0.11         | SPI_SCK        | e-paper                                        | clock SPI                         |
| P0.12         | SPI_MOSI       | e-paper                                        | date SPI                          |
| P0.13         | EPD_BUSY       | e-paper                                        | busy status display               |
| P0.14         | I2C_SCL        | BMA423 / DRV2605 / MAX17048 / BQ25180 / RT6160 | magistrala I2C comuna             |
| P0.15         | I2C_SDA        | BMA423 / DRV2605 / MAX17048 / BQ25180 / RT6160 | magistrala I2C comuna             |
| P0.18 / RESET | RESET          | sistem                                         | reset hardware                    |
| P0.19         | HAPTIC_EN      | DRV2605                                        | enable local pentru blocul haptic |
| P0.26         | SW_UP          | buton                                          | input utilizator                  |
| P0.27         | SW_DN          | buton                                          | input utilizator                  |
| P1.00         | SW_ENT         | buton                                          | input utilizator                  |
| P1.01         | ALERT          | MAX17048                                       | alerta fuel gauge                 |
| P1.09         | SWO            | debug                                          | trace / debugging                 |
| XC1 / XC2     | 32 MHz crystal | clock principal                                | stabilitate si RF                 |
| ANT           | RF             | antena + matching                              | transmisie radio BLE              |
| SWDIO         | debug          | TC2030-IDC                                     | programare / debug                |
| SWDCLK        | debug          | TC2030-IDC                                     | programare / debug                |
| VBUS          | alimentare USB | USB-C                                          | intrare 5 V                       |
| D+ / D-       | USB data       | USB-C prin protectie ESD                       | interfata USB                     |


## PCB

Layer stack

Structura folosita:

Top - componente, rutare semnale, poligoane GND locale

Route2 - plan principal de masa / referinta

Route15 - rutare interna suplimentara pentru semnale si distributie

Bottom - rutare suplimentara si zone GND


## Design log

In procesul de layout au fost acceptate:

1. SMD - via / pin overlap

In zone foarte dense, in special langa capsule mici si in fanout-ul unor componente fine-pitch, anumite vias au fost plasate direct pe pini ai componentelor.

3. Copper width warnings

Pe rail-urile de putere s-a urmarit 0.3 mm acolo unde spatiul permite. In fanout-uri foarte scurte, imediat la iesirea din pini foarte mici sau intre componente apropiate, au ramas segmente locale mai inguste. Distributia principala de putere a fost mentinuta lata, iar exceptiile sunt limitate la zonele de iesire.

4. Board outline / edge related warnings

Elementele de interfata ale produsului, in special anumite butoane si zona conectorului, au fost aliniate mecanic cu produsul final. Acolo unde apar avertizari de proximitate fata de conturul placii, acestea au fost evaluate din punct de vedere mecanic si acceptate daca nu afecteaza functionalitatea.

5. Unghiuri drepte locale
   
Desi rutarea a fost facuta preponderent fara colturi de 90 de grade, au ramas cateva unghiuri drepte locale in zone foarte aglomerate. Acestea au fost mentinute doar acolo unde alternativele cresteau numarul de vias sau complicau excesiv rutarea.

