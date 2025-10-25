# Materi Dasar ESP32

## 1. Dasar-Dasar Pemrograman ESP32

### 💡 Contoh Program 1: Blink LED Internal

#### Kode Program:
```cpp
void setup() {
  pinMode(2, OUTPUT);  // GPIO2 = LED internal di board ESP32
}

void loop() {
  digitalWrite(2, HIGH);  // Nyalakan LED
  delay(1000);            // Tunggu 1 detik
  digitalWrite(2, LOW);   // Matikan LED
  delay(1000);            // Tunggu 1 detik
}
```

#### Penjelasan Kode:
- **`void setup()`**: Fungsi yang dijalankan sekali saat ESP32 pertama kali dinyalakan atau di-reset
- **`pinMode(2, OUTPUT)`**: Mengonfigurasi GPIO pin 2 sebagai output. Parameter pertama adalah nomor pin, parameter kedua adalah mode (OUTPUT/INPUT)
- **`void loop()`**: Fungsi yang dijalankan berulang-ulang setelah `setup()` selesai
- **`digitalWrite(2, HIGH)`**: Memberikan tegangan HIGH (3.3V) pada pin 2, membuat LED menyala
- **`delay(1000)`**: Menghentikan eksekusi program selama 1000 milidetik (1 detik)
- **`digitalWrite(2, LOW)`**: Memberikan tegangan LOW (0V) pada pin 2, membuat LED mati

#### 💬 Hasil:
LED internal berkedip setiap 1 detik (nyala 1 detik, mati 1 detik).

---

## Pertemuan 2: Input & Output Digital

### 🎯 Tujuan
- Menggunakan tombol (push button) sebagai input
- Mengendalikan LED dengan tombol
- Memahami konsep debouncing sederhana

### ⚙️ Komponen di Wokwi
- ESP32
- 1 Push Button
- 1 LED
- 1 Resistor (220 Ω)

**Catatan**: Aktifkan internal pull-up agar tidak perlu resistor tambahan pada tombol.

### 💡 Contoh Program 2: LED ON saat Tombol Ditekan

#### Kode Program:
```cpp
const int ledPin = 2;
const int buttonPin = 4;

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP); // tombol aktif LOW
}

void loop() {
  int tombol = digitalRead(buttonPin); // baca status tombol
  if (tombol == LOW) { // ditekan
    digitalWrite(ledPin, HIGH);
  } else {
    digitalWrite(ledPin, LOW);
  }
}
```

#### Penjelasan Kode:
- **`const int ledPin = 2`**: Mendefinisikan konstanta untuk pin LED (tidak dapat diubah)
- **`const int buttonPin = 4`**: Mendefinisikan konstanta untuk pin tombol
- **`pinMode(buttonPin, INPUT_PULLUP)`**: Mengaktifkan resistor pull-up internal ESP32 (sekitar 45kΩ). Ini membuat pin dalam kondisi HIGH secara default
- **`digitalRead(buttonPin)`**: Membaca status digital pin tombol (HIGH atau LOW)
- **`if (tombol == LOW)`**: Karena menggunakan INPUT_PULLUP, tombol yang ditekan akan memberikan nilai LOW (ground)
- Logika: Jika tombol ditekan (LOW), LED menyala. Jika tidak ditekan (HIGH), LED mati

---

## 🎚️ 3. Analog Input (Sensor Potensiometer)

### 🎯 Tujuan
- Membaca nilai analog menggunakan `analogRead()`
- Menampilkan hasil ke Serial Monitor

### ⚙️ Komponen
- ESP32
- Potensiometer (hubungkan ke pin 34 atau 35)
- LED (opsional)

### 💻 Kode: Membaca Potensiometer

```cpp
const int potPin = 34;

void setup() {
  Serial.begin(115200); // buka komunikasi serial
}

void loop() {
  int nilai = analogRead(potPin);
  Serial.println(nilai);
  delay(200);
}
```

#### Penjelasan Kode:
- **`Serial.begin(115200)`**: Menginisialisasi komunikasi serial dengan baud rate 115200 bps (bit per second)
- **`analogRead(potPin)`**: Membaca nilai analog dari pin 34. ESP32 memiliki ADC (Analog to Digital Converter) 12-bit
- **Rentang nilai**: 0 - 4095 (2^12 = 4096 level)
  - 0 = 0V
  - 4095 = 3.3V
- **`Serial.println(nilai)`**: Mengirim nilai ke Serial Monitor dengan baris baru
- **`delay(200)`**: Menunda 200ms agar output tidak terlalu cepat

### 🧠 Penjelasan
- Nilai ADC ESP32: **0–4095** (0–3.3V)
- Dapat digunakan untuk mengatur kecepatan motor, kecerahan LED, dll.

---

## 4. PWM (Pulse Width Modulation)

### Mengatur Kecerahan LED

#### Kode Program:
```cpp
int ledPin = 2; //deklarasi pin led1

void setup() {
  pinMode(ledPin, OUTPUT); //pin led dijadikan sebagai pin output
  Serial.begin(9600); //memulai komunikasi ke serial monitor
}

void loop() {
  analogWrite(ledPin, 0); //led mati
  delay(2000);
  analogWrite(ledPin, 50); //led nyala redup
  delay(2000);
  analogWrite(ledPin, 255); //led nyala terang
  delay(2000);
  analogWrite(ledPin, 50); //led nyala redup
  delay(2000); //PWM dari mati, nyala redup, terang, redup
}
```

#### Penjelasan Kode:
- **PWM (Pulse Width Modulation)**: Teknik untuk mengontrol daya dengan mengubah duty cycle sinyal digital
- **`analogWrite(pin, value)`**: Menghasilkan sinyal PWM pada pin
  - **value = 0**: LED mati (0% duty cycle)
  - **value = 50**: LED redup (~20% duty cycle)
  - **value = 255**: LED terang penuh (100% duty cycle)
- Nilai PWM: **0-255** (8-bit)
- ESP32 memiliki 16 channel PWM dengan resolusi hingga 16-bit
- Duty cycle = (value/255) × 100%

---

## 💡 Contoh 2: Kontrol Servo Berdasarkan Potensiometer

#### Kode Program:
```cpp
#include <ESP32Servo.h>

Servo myservo;
const int potPin = 34;

void setup() {
  myservo.attach(15); // pin servo di GPIO15
}

void loop() {
  int potValue = analogRead(potPin);
  int angle = map(potValue, 0, 4095, 0, 180);
  myservo.write(angle);
  delay(20);
}
```

#### Penjelasan Kode:
- **`#include <ESP32Servo.h>`**: Mengimpor library untuk mengontrol servo motor
- **`Servo myservo`**: Membuat objek servo dengan nama myservo
- **`myservo.attach(15)`**: Menghubungkan objek servo ke pin GPIO 15
- **`map(potValue, 0, 4095, 0, 180)`**: Fungsi mapping untuk mengkonversi nilai
  - Input: 0-4095 (nilai ADC)
  - Output: 0-180 (sudut servo dalam derajat)
  - Formula: `output = (input - inMin) * (outMax - outMin) / (inMax - inMin) + outMin`
- **`myservo.write(angle)`**: Menggerakkan servo ke sudut tertentu
- **`delay(20)`**: Delay singkat untuk stabilisasi servo

### 🧠 Penjelasan
- `map()` mengubah rentang nilai (0–4095) menjadi (0–180°)
- Servo di Wokwi butuh supply 5V dan sinyal pada pin yang mendukung PWM

---

## 🧩 6. Komunikasi Serial

### 🎯 Tujuan
- Memahami cara berkomunikasi dengan PC via Serial Monitor
- Dapat menerima perintah teks dan mengontrol perangkat (LED/servo)

### 💻 Contoh Kode: Perintah ON/OFF LED

```cpp
const int ledPin = 2;
String input;

void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);
  Serial.println("Ketik ON atau OFF:");
}

void loop() {
  if (Serial.available()) {
    input = Serial.readStringUntil('\n');
    input.trim();

    if (input == "ON") {
      digitalWrite(ledPin, HIGH);
      Serial.println("LED ON");
    } else if (input == "OFF") {
      digitalWrite(ledPin, LOW);
      Serial.println("LED OFF");
    } else {
      Serial.println("Perintah tidak dikenal");
    }
  }
}
```

#### Penjelasan Kode:
- **`String input`**: Variabel untuk menyimpan string input dari serial
- **`Serial.available()`**: Mengecek apakah ada data yang tersedia di buffer serial (return true jika ada)
- **`Serial.readStringUntil('\n')`**: Membaca karakter sampai menemukan newline (\n)
- **`input.trim()`**: Menghapus whitespace (spasi, tab, newline) di awal dan akhir string
- **`if (input == "ON")`**: Membandingkan string input dengan "ON"
- **Flow**:
  1. Cek apakah ada data serial
  2. Baca string sampai enter
  3. Bersihkan spasi
  4. Eksekusi perintah sesuai input

### 🧠 Penjelasan
- `Serial.readStringUntil('\n')` membaca satu baris input
- `.trim()` menghapus spasi/tanda newline
- Gunakan Serial Monitor (baud 115200) untuk mengetik perintah

---

## 🚀 7. PROJECT AKHIR: Kontrol Servo via Perintah Serial

### 🎯 Tujuan
Membuat sistem yang dapat menerima perintah dari Serial Monitor untuk mengontrol posisi servo (simulasi motor DC).

### ⚙️ Komponen Wokwi
- ESP32 DevKit v1
- Servo Motor
- LED indikator (opsional)

**Hubungan pin:**
- Servo → pin 15
- LED → pin 2 (opsional)

### 💻 Kode Lengkap: Kontrol Servo via Serial

```cpp
#include <Servo.h>

Servo motor;
String command;  // perintah dari serial
int angle = 90;  // posisi awal

void setup() {
  Serial.begin(115200);
  motor.attach(15);
  motor.write(angle);
  Serial.println("Ketik perintah: LEFT, RIGHT, CENTER, atau ANGLE <nilai>");
}

void loop() {
  if (Serial.available()) {
    command = Serial.readStringUntil('\n');
    command.trim();

    if (command == "LEFT") {
      angle = 0;
    } else if (command == "RIGHT") {
      angle = 180;
    } else if (command == "CENTER") {
      angle = 90;
    } else if (command.startsWith("ANGLE")) {
      int value = command.substring(6).toInt();
      if (value >= 0 && value <= 180) angle = value;
    } else {
      Serial.println("Perintah tidak dikenal");
      return;
    }

    motor.write(angle);
    Serial.print("Servo di posisi: ");
    Serial.println(angle);
  }
}
```

#### Penjelasan Kode:
- **`motor.write(angle)`**: Menggerakkan servo ke posisi awal (90°)
- **`command.startsWith("ANGLE")`**: Mengecek apakah string dimulai dengan "ANGLE"
- **`command.substring(6)`**: Mengambil substring mulai dari karakter ke-6 (setelah "ANGLE ")
  - Contoh: "ANGLE 45" → substring(6) = "45"
- **`.toInt()`**: Mengkonversi string menjadi integer
- **Validasi**: `if (value >= 0 && value <= 180)` memastikan nilai sudut valid
- **`return`**: Keluar dari fungsi loop() jika perintah tidak dikenal

**Perintah yang didukung:**
- `LEFT` → Servo ke 0°
- `RIGHT` → Servo ke 180°
- `CENTER` → Servo ke 90°
- `ANGLE 45` → Servo ke 45° (atau nilai 0-180)

---

## BONUS: DHT22 + OLED Display

### 💡 Contoh Program 10: DHT22 + OLED Display

#### Kode Program:
```cpp
#include <Wire.h>
#include <Adafruit_SSD1306.h>
#include "DHT.h"

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

#define DHTPIN 15
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
}

void loop() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  display.clearDisplay();
  display.setCursor(0, 0);
  display.println("Sensor DHT22");
  display.print("Suhu: ");
  display.print(t);
  display.println(" C");
  display.print("Lembab: ");
  display.print(h);
  display.println(" %");
  display.display();

  delay(2000);
}
```

#### Penjelasan Kode:
- **`#include <Wire.h>`**: Library untuk komunikasi I2C
- **`#include <Adafruit_SSD1306.h>`**: Library untuk OLED display SSD1306
- **`#include "DHT.h"`**: Library untuk sensor DHT22
- **`#define SCREEN_WIDTH 128`**: Mendefinisikan lebar layar OLED (128 pixel)
- **`#define SCREEN_HEIGHT 64`**: Mendefinisikan tinggi layar OLED (64 pixel)
- **`Adafruit_SSD1306 display(...)`**: Membuat objek display
  - `&Wire`: Pointer ke objek I2C
  - `-1`: Reset pin (tidak digunakan)
- **`#define DHTTYPE DHT22`**: Menentukan tipe sensor (DHT22/DHT11)
- **`DHT dht(DHTPIN, DHTTYPE)`**: Membuat objek sensor DHT
- **`display.begin(SSD1306_SWITCHCAPVCC, 0x3C)`**: 
  - `SSD1306_SWITCHCAPVCC`: Mode power internal
  - `0x3C`: Alamat I2C OLED (biasanya 0x3C atau 0x3D)
- **`dht.readHumidity()`**: Membaca kelembaban (float, satuan %)
- **`dht.readTemperature()`**: Membaca suhu (float, satuan °C)
- **`display.clearDisplay()`**: Membersihkan buffer display
- **`display.setCursor(0, 0)`**: Set posisi kursor (x=0, y=0)
- **`display.println()`**: Menampilkan teks dengan pindah baris
- **`display.display()`**: Menampilkan buffer ke layar OLED (wajib dipanggil!)

**Catatan**: Wokwi otomatis mengenali alamat I2C OLED (0x3C).

---

## Rangkuman Konsep Penting

### 1. Fungsi Dasar Arduino/ESP32
- `pinMode()` - Set mode pin
- `digitalWrite()` - Tulis digital
- `digitalRead()` - Baca digital
- `analogWrite()` - Tulis PWM
- `analogRead()` - Baca analog
- `delay()` - Tunda waktu

### 2. Komunikasi Serial
- `Serial.begin()` - Inisialisasi
- `Serial.print()` - Kirim data
- `Serial.available()` - Cek data tersedia
- `Serial.readStringUntil()` - Baca string

### 3. Tipe Data
- `int` - Integer (-32768 to 32767)
- `float` - Bilangan desimal
- `String` - Teks
- `const` - Konstanta

### 4. Struktur Kontrol
- `if-else` - Percabangan
- `loop()` - Perulangan otomatis
- `for/while` - Perulangan manual

---

**Dibuat untuk pembelajaran ESP32 di Wokwi Simulator**
