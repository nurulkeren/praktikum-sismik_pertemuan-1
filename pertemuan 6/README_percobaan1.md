1. Jelaskan proses bagaimana tombol dapat mengubah kondisi LED menggunakan
interrupt!<br>
jawab:Saat tombol ditekan, pin interrupt pada mikrokontroler mendeteksi perubahan sinyal listrik. Perubahan ini langsung memicu Interrupt Service Routine (ISR) tanpa harus menunggu program utama selesai dijalankan<br>

2. Apa fungsi attachInterrupt() pada program tersebut?<br>
jawab:<br>
Fungsi attachInterrupt() digunakan untuk:<br>
- Menghubungkan pin interrupt dengan fungsi ISR.<br>
- Menentukan kapan interrupt dijalankan.<br>

3. Mengapa pada ISR tidak disarankan menggunakan delay() dan Serial.print()?<br>
jawab:<br>
Karena ISR harus berjalan sangat cepat.<br>

delay()<br>
- Membuat program berhenti sementara.<br>
- Dapat menyebabkan sistem hang atau interrupt lain tidak terbaca.<br>
Serial.print()<br>
- Proses komunikasi serial relatif lambat.<br>
- Bisa menyebabkan buffer penuh atau program tidak stabil.<br>

4. Apa fungsi keyword volatile pada variabel ledState?<br>
jawab: Keyword volatile memberi tahu compiler bahwa nilai variabel bisa berubah sewaktu-waktu di luar program utama, misalnya oleh interrupt.Tanpa volatile, compiler bisa menganggap nilai variabel tidak berubah sehingga pembacaan menjadi tidak akurat.<br>

5. Pada percobaan digunakan mode interrupt FALLING. Modifikasikan program
menggunakan mode interrupt lain (RISING, CHANGE, atau LOW)<br>
jawab: <br>
```cpp
#include <Arduino.h>

volatile bool ledState = false; // Menyimpan status LED dan bisa diubah di ISR

void tombolInterrupt() {
  ledState = !ledState; // Mengubah kondisi LED (ON jadi OFF / OFF jadi ON)
}

void setup() {

  pinMode(13, OUTPUT); // Pin 13 sebagai output LED

  pinMode(2, INPUT_PULLUP); // Pin 2 sebagai input tombol dengan pull-up internal

  attachInterrupt(
    digitalPinToInterrupt(2), // Menggunakan interrupt pada pin 2
    tombolInterrupt,          // Fungsi ISR yang dipanggil
    RISING                    // Interrupt aktif saat LOW -> HIGH
  );
}

void loop() {

  digitalWrite(13, ledState); // Menyalakan LED sesuai nilai ledState
}
```
# Praktikum Interrupt Arduino - Mode RISING

## Deskripsi
Program menggunakan interrupt untuk mengontrol LED dengan push button menggunakan mode interrupt RISING.

## Cara Kerja
- Tombol terhubung ke pin 2.
- LED terhubung ke pin 13.
- Saat tombol dilepas, sinyal berubah dari LOW ke HIGH.
- Perubahan tersebut memicu interrupt.
- ISR akan mengubah status LED.

## Mode Interrupt
Program menggunakan mode:
RISING

Artinya interrupt aktif saat terjadi perubahan sinyal LOW → HIGH.

## Hasil Percobaan
- LED tidak berubah saat tombol ditekan.
- LED berubah saat tombol dilepas.

## Kesimpulan
Mode RISING membuat interrupt bekerja ketika tombol dilepas, berbeda dengan FALLING yang aktif saat tombol ditekan.
