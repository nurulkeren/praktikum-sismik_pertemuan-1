1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!<br>
jawab:Ketiga task pada program FreeRTOS terlihat berjalan secara bersamaan, tetapi sebenarnya dijalankan secara bergantian sangat cepat oleh scheduler FreeRTOS.<br>
Mekanismenya:<br>

Pada setup(), dibuat 3 task menggunakan xTaskCreate():<br>
- TaskBlink1
- TaskBlink2
- Taskprint
Setelah itu vTaskStartScheduler() menjalankan scheduler FreeRTOS.<br>
Scheduler akan mengatur kapan setiap task mendapat giliran menggunakan CPU.<br>
Karena semua task memiliki prioritas yang sama (priority = 1), maka FreeRTOS menggunakan metode round robin scheduling atau pembagian waktu secara bergantian.<br>

2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!<br>
jawab:<br>
Untuk menambahkan task keempat, langkah-langkahnya:<br>

- Tambahkan deklarasi task<br>
void TaskBlink3( void *pvParameters );<br>
- Tambahkan xTaskCreate() pada setup<br>
xTaskCreate(TaskBlink3, "task4", 128, NULL, 1, NULL);<br>
- Buat isi fungsi task<br>

3. Modifikasilah program dengan menambah sensor (misalnya potensiometer), lalu
gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya?<br>
jawab:<br>
```cpp
#include <Arduino_FreeRTOS.h> 
// Memanggil library FreeRTOS agar Arduino dapat menjalankan multitasking

void TaskBlink(void *pvParameters); 
// Deklarasi fungsi task untuk LED

void TaskPot(void *pvParameters);   
// Deklarasi fungsi task untuk membaca potensiometer

int potValue = 0;    
// Variabel untuk menyimpan nilai ADC dari potensiometer

int delayLed = 200;  
// Variabel delay LED, awalnya 200 ms

void setup() {

  Serial.begin(9600);  
  // Memulai komunikasi serial dengan baudrate 9600

  xTaskCreate(
    TaskBlink,     
    "Blink",       
    128,           
    NULL,          
    1,             
    NULL           
  );
  // Membuat task pertama:
  // TaskBlink  -> fungsi task
  // "Blink"    -> nama task
  // 128        -> ukuran stack memory
  // NULL       -> parameter task
  // 1          -> prioritas task
  // NULL       -> task handle

  xTaskCreate(
    TaskPot,       
    "Pot",         
    128,           
    NULL,          
    1,             
    NULL           
  );
  // Membuat task kedua untuk membaca potensiometer

  vTaskStartScheduler();  
  // Menjalankan scheduler FreeRTOS
  // Setelah ini task mulai berjalan bergantian
}

void loop() {
}
// Loop kosong karena semua program dijalankan oleh task FreeRTOS

void TaskBlink(void *pvParameters) {

  pinMode(8, OUTPUT);  
  // Mengatur pin 8 sebagai output LED

  while(1) {           
  // Perulangan tak terbatas agar task terus berjalan

    digitalWrite(8, HIGH);  
    // Menyalakan LED

    vTaskDelay(delayLed / portTICK_PERIOD_MS);  
    // Menunggu sesuai nilai delayLed
    // Selama delay, scheduler dapat menjalankan task lain

    digitalWrite(8, LOW);   
    // Mematikan LED

    vTaskDelay(delayLed / portTICK_PERIOD_MS);  
    // Delay lagi sebelum LED menyala kembali
  }
}

void TaskPot(void *pvParameters) {

  while(1) {            
  // Perulangan terus menerus

    potValue = analogRead(A0);  
    // Membaca nilai analog dari potensiometer di pin A0
    // Nilai ADC berkisar 0–1023

    delayLed = map(potValue, 0, 1023, 100, 1000);  
    // Mengubah nilai ADC menjadi delay
    // 0    -> 100 ms
    // 1023 -> 1000 ms

    Serial.print("Potensiometer: ");  
    // Menampilkan teks ke serial monitor

    Serial.print(potValue);           
    // Menampilkan nilai ADC

    Serial.print(" | Delay LED: ");   
    // Menampilkan teks delay

    Serial.println(delayLed);         
    // Menampilkan nilai delay LED

    vTaskDelay(100 / portTICK_PERIOD_MS);  
    // Delay 100 ms agar pembacaan tidak terlalu cepat
  }
}
```
