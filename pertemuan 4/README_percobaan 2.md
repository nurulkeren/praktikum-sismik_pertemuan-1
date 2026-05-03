1. Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!<br>
jawab: LED dapat diatur kecerahannya menggunakan fungsi analogWrite() karena fungsi ini menghasilkan sinyal PWM (Pulse Width Modulation).<br>
2. Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?<br>
jawab: Nilai ADC dan PWM berbeda skala:<br>
- ADC (analogRead) → 0–1023 (10-bit)<br>
- PWM (analogWrite) → 0–255 (8-bit)<br>

Hubungannya:<br>
Nilai ADC harus dikonversi ke PWM menggunakan map()<br>
3. Modifikasilah program berikut agar LED hanya menyala pada rentang kecerahan sedang, yaitu hanya ketika nilai PWM berada pada rentang 50 sampai 200.<br>
jawab: 
```cpp
#include <Arduino.h>

// ===================== PIN SETUP =====================
const int potPin = A0;   
const int ledPin = 9;   

// ===================== VARIABEL =====================
int nilaiADC = 0;  
int pwm = 0;       

void setup() {

  pinMode(ledPin, OUTPUT);

  Serial.begin(9600);
}

void loop() {

  // ===================== PEMBACAAN SENSOR =====================
  nilaiADC = analogRead(potPin);

  // ===================== PEMROSESAN DATA =====================
  pwm = map(nilaiADC, 0, 1023, 0, 255);

  // ===================== OUTPUT PWM (DIBATASI) =====================
  // LED hanya nyala jika PWM antara 50–200
  if (pwm >= 50 && pwm <= 200) {
    analogWrite(ledPin, pwm);
  } else {
    analogWrite(ledPin, 0); // LED mati di luar rentang
  }

  // ===================== MONITORING DATA =====================
  Serial.print("ADC: ");
  Serial.print(nilaiADC);

  Serial.print(" | PWM: ");
  Serial.println(pwm);

  delay(50);
}
```
