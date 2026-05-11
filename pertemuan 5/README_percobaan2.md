1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!<br>
jawab:<br>
Kedua task pada program FreeRTOS terlihat berjalan bersamaan, tetapi sebenarnya dijalankan secara bergantian oleh scheduler FreeRTOS.<br>

Task yang digunakan:<br>
- read_data<br>
- display<br>

Mekanismenya:<br>
- Scheduler menjalankan task read_data<br>
- Task mengisi data suhu dan kelembaban<br>
- Data dikirim ke queue menggunakan:<br>
xQueueSend(my_queue, &x, portMAX_DELAY);<br>
- Setelah itu task delay:<br>
vTaskDelay(100 / portTICK_PERIOD_MS);<br>
- Saat task delay, scheduler memindahkan CPU ke task display<br>
- Task display membaca data queue menggunakan:<br>
xQueueReceive(my_queue, &x, portMAX_DELAY);<br>
- Data kemudian ditampilkan ke Serial Monitor<br>
Karena perpindahan task berlangsung sangat cepat, kedua task tampak berjalan bersamaan meskipun sebenarnya CPU menjalankan task satu per satu.<br>

2. Apakah program ini berpotensi mengalami race condition? Jelaskan!<br>
jawab:<br>
Program ini tidak terlalu berpotensi mengalami race condition karena komunikasi antar task menggunakan queue FreeRTOS.<br>
Queue berfungsi sebagai media pertukaran data yang aman antar task.<br>
Pada program:<br>
- Task read_data hanya mengirim data ke queue<br>
- Task display hanya menerima data dari queue<br>

Tidak ada variabel global yang diakses langsung secara bersamaan oleh kedua task.<br>

4. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga
informasi yang ditampilkan dinamis. Bagaimana hasilnya?<br>
jawab:<br>
```cpp
#include <Arduino_FreeRTOS.h>  
// Library FreeRTOS untuk menjalankan multitasking

#include <queue.h>             
// Library queue FreeRTOS untuk komunikasi antar task

#include <DHT.h>               
// Library sensor DHT11/DHT22

#define DHTPIN 2               
// Menentukan pin data DHT di pin 2

#define DHTTYPE DHT11          
// Menentukan jenis sensor yang digunakan yaitu DHT11

DHT dht(DHTPIN, DHTTYPE);      
// Membuat objek sensor DHT

// ===================== STRUKTUR DATA =====================
struct readings {
  float temp;                  
  // Variabel suhu

  float h;                     
  // Variabel kelembaban
};

// ===================== HANDLE QUEUE =====================
QueueHandle_t my_queue;        
// Membuat handle queue untuk komunikasi antar task

// ===================== DEKLARASI TASK =====================
void read_data(void *pvParameters);  
// Deklarasi task pembacaan sensor

void display(void *pvParameters);    
// Deklarasi task menampilkan data

void setup() {

  Serial.begin(9600);          
  // Memulai komunikasi serial baudrate 9600

  dht.begin();                 
  // Mengaktifkan sensor DHT

  my_queue = xQueueCreate(1, sizeof(struct readings));
  // Membuat queue
  // Kapasitas queue = 1 data
  // Ukuran data sesuai struct readings

  xTaskCreate(
    read_data,                 
    "Read Sensors",            
    128,                       
    NULL,                      
    1,                         
    NULL                       
  );
  // Membuat task pembacaan sensor
  // read_data    = fungsi task
  // "Read Sensors" = nama task
  // 128          = ukuran stack
  // NULL         = parameter task
  // 1            = prioritas task
  // NULL         = task handle

  xTaskCreate(
    display,                   
    "Display",                 
    128,                       
    NULL,                      
    1,                         
    NULL                       
  );
  // Membuat task display data
}

void loop() {
}
// Loop kosong karena program dijalankan oleh FreeRTOS

// ===================== TASK READ =====================
void read_data(void *pvParameters) {

  struct readings x;           
  // Membuat variabel struktur untuk menyimpan data sensor

  for (;;) {                   
  // Perulangan tak terbatas

    x.temp = dht.readTemperature();
    // Membaca suhu dari sensor DHT11

    x.h = dht.readHumidity();
    // Membaca kelembaban dari sensor DHT11

    xQueueSend(my_queue, &x, portMAX_DELAY);
    // Mengirim data struktur x ke queue
    // portMAX_DELAY artinya task menunggu jika queue penuh

    vTaskDelay(2000 / portTICK_PERIOD_MS);
    // Delay 2 detik sebelum membaca sensor lagi
  }
}

// ===================== TASK DISPLAY =====================
void display(void *pvParameters) {

  struct readings x;           
  // Variabel struktur untuk menerima data dari queue

  for (;;) {                   
  // Perulangan terus menerus

    if (xQueueReceive(my_queue, &x, portMAX_DELAY) == pdPASS) {
    // Mengambil data dari queue
    // Jika berhasil maka menjalankan isi if

      Serial.print("Temperature = ");
      // Menampilkan teks Temperature

      Serial.print(x.temp);
      // Menampilkan nilai suhu

      Serial.println(" C");
      // Menampilkan satuan Celcius

      Serial.print("Humidity = ");
      // Menampilkan teks Humidity

      Serial.print(x.h);
      // Menampilkan nilai kelembaban

      Serial.println(" %");
      // Menampilkan satuan persen

      Serial.println("----------------");
      // Garis pemisah output
    }
  }
}
```


