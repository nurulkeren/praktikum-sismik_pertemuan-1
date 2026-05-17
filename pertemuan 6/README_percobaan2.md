1. Jelaskan bagaimana fungsi millis() bekerja pada program tersebut!<br>
jawab:Fungsi millis() digunakan untuk menghitung waktu sejak Arduino mulai dijalankan.
Nilai yang dikembalikan berupa milidetik (ms)<br>

2. Apa perbedaan utama antara delay() dan millis()?<br>
jawab:<br>
delay()<br>
- Program berhenti sementara<br>
- Bersifat blocking<br>
- Tidak bisa menjalankan tugas lain saat delay<br>
- Lebih sederhana<br>

millis()<br>
- Program tetap berjalan<br>
- Bersifat non-blocking<br>
- Bisa menjalankan beberapa tugas sekaligus<br>
- Lebih fleksibel<br>

3. Mengapa metode millis() disebut non-blocking?<br>
jawab: Karena saat menunggu waktu tertentu, program tidak berhenti.
Arduino tetap menjalankan kode lain di dalam loop()<br>

4. Modifikasi program agar:<br>
• LED pertama berkedip setiap 1 detik<br>
• LED kedua berkedip setiap 500 ms<br>
• Tanpa menggunakan delay()<br>

jawab: <br>
```cpp
#include <Arduino.h>

unsigned long previousMillis1 = 0; // Waktu terakhir LED1 berubah
unsigned long previousMillis2 = 0; // Waktu terakhir LED2 berubah

const long interval1 = 1000; // Interval LED1 = 1 detik
const long interval2 = 500;  // Interval LED2 = 500 ms

bool ledState1 = false; // Status LED1
bool ledState2 = false; // Status LED2

void setup() {

  pinMode(13, OUTPUT); // Pin 13 sebagai output LED pertama

  pinMode(12, OUTPUT); // Pin 12 sebagai output LED kedua
}

void loop() {

  unsigned long currentMillis = millis(); // Mengambil waktu saat ini

  // LED pertama berkedip setiap 1 detik
  if(currentMillis - previousMillis1 >= interval1) {

    previousMillis1 = currentMillis; // Simpan waktu terakhir LED1 berubah

    ledState1 = !ledState1; // Mengubah status LED1

    digitalWrite(13, ledState1); // Menyalakan/mematikan LED1
  }

  // LED kedua berkedip setiap 500 ms
  if(currentMillis - previousMillis2 >= interval2) {

    previousMillis2 = currentMillis; // Simpan waktu terakhir LED2 berubah

    ledState2 = !ledState2; // Mengubah status LED2

    digitalWrite(12, ledState2); // Menyalakan/mematikan LED2
  }
}
```


