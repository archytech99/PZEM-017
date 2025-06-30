# PZEM-017 DC Communication Module

Pustaka komunikasi arduino untuk monitor power dan energi dari perangkat Peacefair PZEM-017, dengan sedikit perubahan dari pustaka [Hemmarit Hema's PZEM-017-v1](https://github.com/maxzerker/PZEM-017-v1).

***

## Rentang Pengukuran

50A, 100A, 200A, 300A kisaran arus tergantung pada spesifikasi shunt eksternal.

## Fitur ([*datasheet*](./PZEM-003%20017User%20Manual(MEDC300V).pdf))

### Tegangan (V)

- Rentang pengukuran: 0,05-300 V. (*bila tegangan uji < 7V, harap gunakan mode catu daya independen*)
- Resolusi: 0,01V.
- Akurasi pengukuran: 1%.

### Arus (A)

- Rentang pengukuran: 0,02-300 A (*dapat dicocokkan dengan empat jenis shunt 50, 100, 200, 300A*)
- Resolusi: 0,01A.
- Akurasi pengukuran: 1%.

### Daya (W)

- Rentang pengukuran: 0,2-90 kW.
- Resolusi: 0,1W.
- Akurasi pengukuran: 1%.

### Konsumsi Energi (Wh)

- Rentang pengukuran: 0-9999 kWh.
- Resolusi: 1Wh.
- Akurasi pengukuran: 1%.

### Alarm Tegangan Berlebih

- Ambang tegangan dapat diatur, dibagi menjadi ambang tegangan tinggi dan ambang tegangan rendah, ketika tegangan yang diukur melebihi ambang batas, maka dapat membunyikan alarm. Ambang tegangan tinggi default adalah 300V, ambang tegangan rendah default adalah 7V.

## Protokol Komunikasi

- Antarmuka **RS485**.
- Lapisan fisik menggunakan antarmuka komunikasi UART ke RS485.
- Baud rate adalah **9600**, 8 bit data, 2 bit stop, tanpa paritas.

## Daftar alamat pengukuran disusun sebagai berikut

| Register Addr. | Deskripsi                     | Resolution                             |
|----------------|-------------------------------|----------------------------------------|
| 0x0000         | nilai tegangan                | 1LSB sesuai dengan 0,01V               |
| 0x0001         | nilai arus                    | 1LSB sesuai dengan 0,01A               |
| 0x0002         | nilai daya rendah (16 bits)   | 1LSB sesuai dengan 0,1W                |
| 0x0003         | nilai daya tinggi (16 bits)   | 1LSB sesuai dengan 0,1W                |
| 0x0004         | nilai energi rendah (16 bits) | 1LSB sesuai dengan 1Wh                 |
| 0x0005         | nilai energi tinggi (16 bits) | 1LSB sesuai dengan 1Wh                 |
| 0x0006         | alarm status tegangan tinggi  | 0xFFFF adalah alarm, 0x000 bukan alarm |
| 0x0007         | alarm status tegangan rendah  | 0xFFFF adalah alarm, 0x000 bukan alarm |

\* *Menggunakan transformator arus eksternal sebagai pengganti shunt internal*

## Contoh Sketch

```c++
/* Gunakan software serial untuk PZEM
 * Pin 4 Rx (Hubungkan ke Tx pin pada PZEM)
 * Pin 5 Tx (Hubungkan ke Rx pin pada PZEM)
*/
#include <PZEM017v1.h>
PZEM017v1 pzem(4, 5); //WMOS D1 4=D1, 5=D2

void setup()
{
  Serial.begin(115200);
}

void loop()
{
  float voltage = pzem.voltage();
  if ( !isnan(voltage) )
    Serial.printf("Tegangan: %.3f V", voltage);
  else
    Serial.println("Kesalahan membaca tegangan");

  float current = pzem.current();
  if ( !isnan(current) )
    Serial.print("Arus: %.3f A", current);
  else
    Serial.println("Kesalahan membaca arus");

  float power = pzem.power();
  if ( !isnan(power) )
    Serial.print("Daya: %.2f W", power);
  else
    Serial.println("Kesalahan membaca daya");

  float energy = pzem.energy();
  if ( !isnan(energy) )
    Serial.print("Energi: %.3f kWh");
  else
    Serial.println("Kesalahan membaca energi");

  Serial.println();
  delay(2000);
}
```

## Petunjuk Instalasi

Anda seharusnya dapat menginstal pustaka dari Library Manager di Arduino IDE. Anda juga dapat mengunduh ZIP repositori ini dan menginstalnya secara manual. Panduan tentang cara melakukannya ada di sini: [https://www.arduino.cc/en/guide/libraries](https://www.arduino.cc/en/guide/libraries).

***
**Credits**: thank you to [Hemmarit Hema's PZEM-017-v1](https://github.com/maxzerker/PZEM-017-v1) for this great library.
