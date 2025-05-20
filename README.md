//Bağlantılar:
//HC-SR04:

//VCC → 5V

//GND → GND

//TRIG → D9

//ECHO → D10

//OLED (I2C):

//VCC → 5V

//GND → GND

//SDA → A4

//SCL → A5






#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

#define TRIG_PIN 9
#define ECHO_PIN 10

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

void setup() {
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  Serial.begin(9600);

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("OLED ekran yok, çek fişi tak tekrar dene");
    for (;;);
  }

  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.display();
}

long getDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH);
  long distance = duration * 0.034 / 2;

  return distance;
}

void loop() {
  long distance = getDistance();

  int barLength = map(distance, 2, 100, SCREEN_WIDTH, 0); // 2cm'den 100cm'ye kadar ölçekle

  display.clearDisplay();

  // Mesafe yazısı
  display.setCursor(0, 0);
  display.setTextSize(1);
  display.print("Mesafe: ");
  display.print(distance);
  display.println(" cm");

  // Alt kısımda bar çiz
  display.fillRect(0, SCREEN_HEIGHT - 10, barLength, 8, SSD1306_WHITE);

  display.display();

  delay(100);
}
