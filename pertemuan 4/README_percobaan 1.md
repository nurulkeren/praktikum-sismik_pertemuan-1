1. Apa fungsi perintah analogRead() pada rangkaian praktikum ini?<br>
jawab: Fungsi analogRead() digunakan untuk membaca nilai tegangan analog dari pin input (misalnya dari potensiometer).<br>
- Output berupa nilai digital dengan rentang 0–1023<br>
- Nilai ini merepresentasikan tegangan 0V – 5V<br>

2. Mengapa diperlukan fungsi map() dalam program tersebut?<br>
jawab: Fungsi map() digunakan untuk mengubah rentang nilai dari satu skala ke skala lain<br>

3. Modifikasi program berikut agar servo hanya bergerak dalam rentang 30° hingga 150°, meskipun potensiometer tetap memiliki rentang ADC 0–1023. <br>
jawab: <br>

```cpp
#include <Servo.h> // library untuk servo motor

Servo myservo; 

// ===================== PIN SETUP =====================
const int potensioPin = A0;   
const int servoPin = 9;      

// ===================== VARIABEL =====================
int pos = 0; 
int val = 0; 

void setup() {

  myservo.attach(servoPin);  

  Serial.begin(9600); 

}

void loop() {

  // ===================== PEMBACAAN ADC =====================
  val = analogRead(potensioPin);

  // ===================== KONVERSI DATA =====================
  // Mapping diubah dari 0–180 menjadi 30–150
  pos = map(val, 0, 1023, 30, 150);

  // ===================== OUTPUT SERVO =====================
  myservo.write(pos);

  // ===================== MONITORING DATA =====================
  Serial.print("ADC Potensio: ");
  Serial.print(val);

  Serial.print(" | Sudut Servo: ");
  Serial.println(pos);

  // ===================== STABILISASI =====================
  delay(15);
}
```
