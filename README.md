#include <LiquidCrystal_I2C.h>
#include <Wire.h>
#include <EEPROM.h>

int address_eeprom = 0x71;
String tampilan = "utama";
int i = 0;
int fPaus = 0;
int pot = 35;
int nilai;
int relay = 18;
int relay1 = 19;
int relay2 = 5;
int led = 17;

int btn_up = 26;
int btn_down = 33;
int btn_menu = 27;
int btn_mode = 25;
int main_menu = 1;
int select_menu = 0;
int sub_menu = 0;
int main_menu_washer = 1;
int select_menu_washer = 0;
int sub_menu_washer = 0;
int select_mode =0;
int sub_mode = 0;
int sub_page = 0;
int select_setting = 0;
int menu_setting = 1;
int sub_setting = 0;

int counter_target;
int menit_target;
int detik_target;
int menit_mati_target;
int detik_mati_target;

int menit_washer;
int detik_washer;
int millidetik_washer;
int detik_mati_washer;
int menit_mati_washer;
int millidetik_mati_washer;

long lastButton = 0;
long delayAntiBouncing = 50;
long dataPaus = 0;
unsigned long mulai, selesai, dataStopWatch;
unsigned long mulaiStopwatchKedua;
unsigned long akhir, selesai1, dataStopWatch1;
unsigned long blink_previousMillis;
boolean blink_state = false;
int siklus = 0;

float lastmenit = 0;
float lastdetik = 0;
float lastmillidetik = 0;
float lastmenit1 = 0;
float lastdetik1 = 0;
float lastmillidetik1 = 0;

float menit, detik, millidetik, menit1, detik1, millidetik1;
unsigned long over;
unsigned long over1;
int count = 0;
int hasil;
int lasthasil;

bool status_flow = false;
bool status_spin = false;
bool status_washer = false;


LiquidCrystal_I2C lcd(0x27, 20, 4);

byte menu_RIGHT[8] = {
  B10000,
  B11000,
  B11100,
  B11110,
  B11100,
  B11000,
  B10000,
  B00000
};

byte menu_LEFT[8] = {
  B00001,
  B00011,
  B00111,
  B01111,
  B00111,
  B00011,
  B00001,
  B00000
};

byte Loading_BAR[8] = {
  B11111,
  B11111,
  B11111,
  B11111,
  B11111,
  B11111,
  B11111,
  B11111
};

void setup() {
  Serial.begin(115200);
  EEPROM.begin(512);
  lcd.init();

  lcd.backlight();

  pinMode(btn_up, INPUT_PULLUP);
  pinMode(btn_down, INPUT_PULLUP);
  pinMode(btn_mode, INPUT_PULLUP);
  pinMode(btn_menu, INPUT_PULLUP);
  pinMode(led, OUTPUT);
  pinMode(relay, OUTPUT);
  pinMode(relay1, OUTPUT);
  pinMode(relay2, OUTPUT);
  digitalWrite(relay, LOW);
  digitalWrite(relay1, LOW);
  digitalWrite(relay2, LOW);

  lcd.createChar(4, Loading_BAR);
  lcd.createChar(5, menu_RIGHT);
  lcd.createChar(6, menu_LEFT);

  lcd.clear();
  lcd.setCursor(3, 0);
  lcd.print("W E L C O M E");
  lcd.setCursor(2, 1);
  lcd.print("LAUNDRY  SYSTEM");
  delay(1500);
  lcd.clear();
  lcd.setCursor(6, 0);
  lcd.print("Loading");
  lcd.setCursor(0, 1);
  for (int thisChar = 0; thisChar < 20; thisChar++) {
    lcd.write(4);
    delay(50);
  }
  lcd.clear();
  delay (500);
  tampilan = "mode";
  select_mode = 0;
  sub_mode = 1;
}

void loop() {
  cek_button();
  display();



}

void cek_button() {
  if (tampilan == "utama") {
    if (digitalRead(btn_mode) == LOW) {
      Serial.println("mode");
      tampilan = "mode";
      sub_mode = 3;
      select_mode = 0;
      lcd.clear();
      delay(50);
    }
  }
  else if (tampilan == "life") {
    if (digitalRead(btn_mode) == LOW) {
      Serial.println("mode");
      tampilan = "mode";
      sub_mode = 4;
      select_mode = 0;
      lcd.clear();
      delay(50);
    }
   else if (digitalRead(btn_menu) == LOW){
        Serial.println("menu");
        lcd.clear();
        delay(150);
        tampilan = "menu";
        sub_menu = 0;
        main_menu = 1;
        select_menu = 0;
      }
    else if (digitalRead(btn_up) == LOW){
      if (sub_page == 0){
        lcd.clear();
        delay(150);
        sub_page = 1;
      }
      else if (sub_page == 1){
        lcd.clear();
        delay(150);
        tampilan = "life";
        sub_page = 0;
        }
      }
      else if (digitalRead(btn_down) == LOW) {
        if (i == 0) {
          mulai = millis();
          fPaus = 0;
          i = 1;
        }

        else if (i == 1) {
        dataPaus = dataStopWatch;
        fPaus = 1;
        i = 0;
        }
      } 

      else if (digitalRead(btn_down) == LOW) {
        if (fPaus == 1){
        dataStopWatch = 0;
        dataPaus = 0;
        lcd.clear();
        lcd.setCursor(15, 2);
        lcd.print("0.0");
        fPaus = 2;
      }
    }
  }
  else if (tampilan == "setting"){
    if (digitalRead(btn_mode) == LOW){
      if (sub_setting == 0){
      Serial.println("mode");
      tampilan = "mode";
      sub_mode = 6;
      select_mode = 0;
      lcd.clear();
      delay(50);
      }
    }
    if (digitalRead(btn_menu) == LOW){
      Serial.print("Setting");
      if (select_setting == 0){
        if (menu_setting == 1){
          lcd.clear();
          delay(150);
          select_setting = 1;
          sub_setting = 1;
        }
        else if (menu_setting == 2){
          lcd.clear();
          delay(150);
          select_setting = 2;
          sub_setting = 1;
        }
        else if (menu_setting == 3){
          lcd.clear();
          delay(150);
          select_setting = 3;
          sub_setting = 1;
        }
      }
      else if (select_setting == 1){
        if (sub_setting == 1){
          lcd.clear();
          lcd.setCursor(7, 0);
          lcd.print("SETTING");
          lcd.setCursor(7, 2);
          lcd.print("(Save)");
          delay(1200);
          EEPROM.write(19, status_flow);
          EEPROM.commit();
          delay(50);
          lcd.clear();
          delay(50);
          tampilan = "setting";
          select_setting = 0;
          sub_setting = 0;
          menu_setting = 1;
        }
      }
      else if (select_setting == 2){
        if (sub_setting == 1){
          lcd.clear();
          lcd.setCursor(7, 0);
          lcd.print("SETTING");
          lcd.setCursor(7, 2);
          lcd.print("(Save)");
          delay(1200);
          EEPROM.write(19, status_spin);
          EEPROM.commit();
          delay(50);
          lcd.clear();
          delay(50);
          tampilan = "setting";
          select_setting = 0;
          sub_setting = 0;
          menu_setting = 2;
        }
      }
      else if (select_setting == 3){
        if (sub_setting == 1){
          lcd.clear();
          lcd.setCursor(7, 0);
          lcd.print("SETTING");
          lcd.setCursor(7, 2);
          lcd.print("(Save)");
          delay(1200);
          EEPROM.write(21, status_washer);
          EEPROM.commit();
          delay(50);
          lcd.clear();
          delay(50);
          tampilan = "setting";
          select_setting = 0;
          sub_setting = 0;
          menu_setting = 3;
        }
      }  
    }
    else if (digitalRead(btn_up) == LOW){
      if(select_setting == 0){
        if (menu_setting == 1){
          lcd.clear();
          delay(50);
          menu_setting = 2;
        }
        else if (menu_setting == 2){
          lcd.clear();
          delay(50);
          menu_setting = 3;
        }
      }
      else if (select_setting == 1){
        if (sub_setting == 1){
          lcd.clear();
          delay(50);
          status_flow = true;
        }
      }
      else if (select_setting == 2){
        if (sub_setting == 1){
          lcd.clear();
          delay(50);
          status_spin = true;
        }
      }
      else if (select_setting == 3){
        if (sub_setting == 1){
          lcd.clear();
          delay(50);
          status_washer = true;
        }
      }
    }
    else if (digitalRead(btn_down) == LOW){
      if (select_setting == 0){
        if (menu_setting == 3){
          lcd.clear();
          delay(50);
          menu_setting = 2;
        }
        else if (menu_setting == 2){
          lcd.clear();
          delay(50);
          menu_setting = 1;
        }
      }
      if (select_setting == 1){
        if (sub_setting == 1){
          lcd.clear();
          delay(50);
          status_flow = false;
        }
      }
      if (select_setting == 2){
        if (sub_setting == 1){
          lcd.clear();
          delay(50);
          status_spin = false;
        }
      }
      if (select_setting == 3){
        if (sub_setting == 1){
          lcd.clear();
          delay(50);
          status_washer = false;
        }
      }
    }
  }
  else if (tampilan == "washer") {
    if (digitalRead(btn_mode) == LOW) {
      Serial.println("mode");
      tampilan = "mode";
      sub_mode = 5;
      select_mode = 0;
      lcd.clear();
      delay(50);
    }
    else if (digitalRead(btn_menu) == LOW){
        Serial.println("menu washer");
        lcd.clear();
        delay(150);
        tampilan = "menu_washer";
        sub_menu_washer = 0;
        main_menu_washer = 1;
        select_menu_washer = 0;
      }
    else if (digitalRead(btn_down) == LOW) {
      if (i == 0) {
        mulai = millis();
        fPaus = 0;
      }

      else if (i == 1) {
      dataPaus = dataStopWatch;
      fPaus = 1;
      }
      i = !i;
    }
  }
  else if (tampilan == "mode") {
    if (digitalRead(btn_menu) == LOW) {
      Serial.println("pilih mode");
      if (select_mode == 0) {
        if (sub_mode == 1) {
          lcd.clear();
          delay(50);
        }
        else if (sub_mode == 2) {
          lcd.clear();
          delay(50);
        }
        else if (sub_mode == 3) {
          select_mode = 1;
          lcd.clear();
          delay(50);
        }
        else if (sub_mode == 4) {
          select_mode = 2;
          lcd.clear();
          delay(50);
        }
        else if (sub_mode == 5) {
          select_mode = 3;
          lcd.clear();
          delay(50);
        }
        else if (sub_mode == 6) {
          select_mode = 4;
          lcd.clear();
          delay(50);
        }
      }
      else {
        if (select_mode == 1 or select_mode == 2 or select_mode == 3 or select_mode == 4){
          if (sub_mode >= 3){
            if (select_mode == 1){
              lcd.setCursor(8, 0);
              lcd.print("MODE");
              lcd.setCursor(5, 2);
              lcd.print("Flow  Rate");
              delay(1200);
              lcd.clear();
              delay (100);
              tampilan = "utama";
            }
            else if (select_mode == 2){
              lcd.setCursor(8, 0);
              lcd.print("MODE");
              lcd.setCursor(3, 2);
              lcd.print("Spin Life Test");
              delay(1200);
              lcd.clear();
              delay (100);
              tampilan = "life";
            }
            else if (select_mode == 3){
              lcd.setCursor(8, 0);
              lcd.print("MODE");
              lcd.setCursor(2, 2);
              lcd.print("Washer Life Test");
              delay(1200);
              lcd.clear();
              delay (100);
              tampilan = "washer";
            }
            else if (select_mode == 4){
              lcd.setCursor(8, 0);
              lcd.print("MODE");
              lcd.setCursor(7, 2);
              lcd.print("Setting");
              delay(1200);
              lcd.clear();
              delay (100);
              tampilan = "setting";
              menu_setting = 1;
            }
            select_mode = 0;
            sub_mode = 0;
          }
          else {
            sub_mode++;
          }
        }
      }
    }
    else if (digitalRead(btn_up) == LOW ){
      if (select_mode == 0){
        if (sub_mode == 1){
          lcd.clear();
          delay(50);
          sub_mode = 2;
        }
        else if (sub_mode == 2){
          lcd.clear();
          delay(50);
          sub_mode = 3;
        }
        else if (sub_mode == 3){
          lcd.clear();
          delay(50);
          sub_mode = 4;
        }
        else if (sub_mode == 4){
          lcd.clear();
          delay(50);
          sub_mode = 5;
        }
        else if (sub_mode == 5){
          lcd.clear();
          delay(50);
          sub_mode = 6;
        }
      }
    }

    else if (digitalRead(btn_down) == LOW){
      if (select_mode == 0){
        if (sub_mode == 6){
          lcd.clear();
          delay(50);
          sub_mode = 5;
        }
        else if (sub_mode == 5){
          lcd.clear();
          delay(50);
          sub_mode = 4;
        }
        else if (sub_mode == 4){
          lcd.clear();
          delay(50);
          sub_mode = 3;
        }
        else if (sub_mode == 3){
          lcd.clear();
          delay(50);
          sub_mode = 2;
        }
        else if (sub_mode == 2){
          lcd.clear();
          delay(50);
          sub_mode = 1;
        }
      }
    }
  }
  else if (tampilan == "menu") {
    if (digitalRead(btn_menu) == LOW) {
      Serial.println("menu");
      if (select_menu == 0) {
        if (sub_menu == 0){
          if (main_menu == 1) {
            lcd.clear();
            delay(150);
            sub_menu = 1;
            select_menu = 1;
          }
          else if (main_menu == 2) {
            lcd.clear();
            delay(150);
            sub_menu = 2;
            select_menu = 2;
          }
          else if (main_menu == 3) {
            lcd.clear();
            delay(50);
            select_menu = 3;
          }
        }  
      }
      else if (select_menu == 1){
        if (sub_menu == 1){
          lcd.clear();
          delay(150);
          sub_menu = 2;
          select_menu = 1;
        }
        else if (sub_menu == 2){
          lcd.clear();
          delay(150);
          sub_menu = 1;
          select_menu = 4;
        }
      }
      else if (select_menu == 4){
        if (sub_menu == 1){
          lcd.clear();
          delay(150);
          sub_menu = 2;
          select_menu = 4;
        }
        else if (sub_menu == 2){
              lcd.clear();
              lcd.setCursor(5, 0);
              lcd.print("Set  Timer");
              lcd.setCursor(7, 2);
              lcd.print("(Save)");
              delay(1200);
              EEPROM.write(1, menit_target);
              EEPROM.write(2, detik_target);
              EEPROM.write(3, menit_mati_target);
              EEPROM.write(4, detik_mati_target);
              EEPROM.commit();
              delay(50);
              lcd.clear();
              delay(50);
              tampilan = "life";
        }
      }
      else {
        if (select_menu == 2){
          if (sub_menu >= 2){
            lcd.clear();
            lcd.setCursor(3, 0);
            lcd.print("Counter Target");
            lcd.setCursor(7, 2);
            lcd.print("(Save)");
            delay(1200);
            EEPROM.write(5 , (counter_target >> 24) & 0xFF);
            EEPROM.write(6, (counter_target >> 16) & 0xFF);
            EEPROM.write(7, (counter_target >> 8) & 0xFF);
            EEPROM.write(8, counter_target & 0xFF);
            EEPROM.commit();
            delay(50);
            lcd.clear();
            delay(50);
            tampilan = "life";
            select_menu = 0;
            sub_menu = 0;
          }
          else {
            sub_menu++;
          }
        }
      if (select_menu == 3){
        if (sub_menu == 0){
          lcd.setCursor(7, 2);
          lcd.print("(Exit)");
          delay(1200);
          lcd.clear();
          delay(50);
          tampilan = "life";
         }
       }
     }
   }
    if (digitalRead(btn_mode) == LOW){
      if (select_menu == 1){
        if (sub_menu == 2){
          lcd.clear();
          delay(150);
          select_menu = 1;
          sub_menu =1;
        }
      }
      else if (select_menu == 4){
        if (sub_menu == 2){
          lcd.clear();
          delay(150);
          select_menu = 4;
          sub_menu =1;
        }
      }
    }
    if (digitalRead(btn_up) == LOW ){
      if (select_menu == 0){
        if (main_menu == 1){
          lcd.clear();
          delay(150);
          main_menu = 2;
        }
        else if (main_menu == 2) {
          lcd.clear();
          delay(150);
          main_menu = 3;
        }
      }
      else if (select_menu == 1){
        if (sub_menu == 1){
          menit_target++;
          if (menit_target > 59) menit_target = 0;
        }
        else if (sub_menu == 2){
          detik_target++;
          if (detik_target > 59) detik_target = 0;
        }
      }
      else if (select_menu == 2){
        if (sub_menu == 2){
          counter_target++;
          if (counter_target > 99999) counter_target = 0;
        }
      }
      else if (select_menu == 4){
        if (sub_menu == 1){
          menit_mati_target++;
          if (menit_mati_target > 59) menit_mati_target = 0;
        }
        else if (sub_menu == 2){
          detik_mati_target++;
          if (detik_mati_target > 59) detik_mati_target = 0;
        }
      }
    }
    if (digitalRead(btn_down) == LOW){
      if (select_menu == 0){
        if (main_menu == 3){
          lcd.clear();
          delay(150);
          main_menu = 2;
        }
        else if (main_menu == 2) {
          lcd.clear();
          delay(150);
          main_menu = 1;
        }
      }
      else if (select_menu == 1){
        if (sub_menu == 1){
          menit_target--;
          if (menit_target < 0) menit_target = 59;
        } 
        else if (sub_menu == 2){
          detik_target--;
          if (detik_target < 0) detik_target = 59;
        }
      }
      else if (select_menu == 2){
        if (sub_menu == 2){
          counter_target--;
          if (counter_target < 0) counter_target = 99999999;
        }
      }
      else if (select_menu == 4){
        if (sub_menu == 1){
          menit_mati_target--;
          if (menit_mati_target < 0) menit_mati_target = 59;
        }
        else if (sub_menu == 2){
          detik_mati_target--;
          if (detik_mati_target < 0) detik_mati_target = 59;
        }
      }
    }
  }
  else if (tampilan == "menu_washer") {
    if (digitalRead(btn_menu) == LOW) {
      Serial.println("menu washer");
      if (select_menu_washer == 0) {
        if (sub_menu_washer == 0){
          if (main_menu_washer == 1) {
            lcd.clear();
            delay(150);
            sub_menu_washer = 1;
            select_menu_washer = 1;
          }
          else if (main_menu_washer == 2) {
            lcd.clear();
            delay(150);
            select_menu_washer = 3;
          }
        }  
      }
      else if (select_menu_washer == 1){
        if (sub_menu_washer == 1){
          lcd.clear();
          delay(150);
          sub_menu_washer = 2;
          select_menu_washer = 1;
        }
        else if (sub_menu_washer == 2){
          lcd.clear();
          delay(150);
          sub_menu_washer = 3;
          select_menu_washer = 1;
        }
        else if (sub_menu_washer == 3){
          lcd.clear();
          delay(150);
          sub_menu_washer = 1;
          select_menu_washer = 2;
        }
      }
      else if (select_menu_washer == 2){
        if (sub_menu_washer == 1){
          lcd.clear();
          delay(150);
          sub_menu_washer = 2;
          select_menu_washer = 2;
        }
        else if (sub_menu_washer == 2){
          lcd.clear();
          delay(150);
          sub_menu_washer = 3;
          select_menu_washer = 2;
        }
        else if (sub_menu_washer == 3){
              lcd.clear();
              lcd.setCursor(5, 0);
              lcd.print("Set  Timer");
              lcd.setCursor(7, 2);
              lcd.print("(Save)");
              delay(1200);
              EEPROM.write(9, menit_washer);
              EEPROM.write(10, detik_washer);
              EEPROM.write(11, millidetik_washer);
              EEPROM.write(12, menit_mati_washer);
              EEPROM.write(13, detik_mati_washer);
              EEPROM.write(14, millidetik_mati_washer);
              EEPROM.commit();
              delay(50);
              lcd.clear();
              delay(50);
              tampilan = "washer";
        }
      }
      else if (select_menu_washer == 3){
        if (sub_menu == 0){
          lcd.setCursor(7, 2);
          lcd.print("(Exit)");
          delay(1200);
          lcd.clear();
          delay(50);
          tampilan = "washer";
         }
       }
    }
    if (digitalRead(btn_mode) == LOW){
      if (select_menu_washer == 1){
        if (sub_menu_washer == 2){
          lcd.clear();
          delay(150);
          select_menu_washer = 1;
          sub_menu_washer = 1;
        }
        else if (sub_menu_washer == 3){
          lcd.clear();
          delay(150);
          select_menu_washer = 1;
          sub_menu_washer = 2;
        }
      }
      else if (select_menu_washer == 2){
        if (sub_menu_washer == 2){
          lcd.clear();
          delay(150);
          select_menu_washer = 2;
          sub_menu_washer = 1;
        }
        else if (sub_menu_washer == 3){
          lcd.clear();
          delay(150);
          select_menu_washer = 2;
          sub_menu_washer = 2;
        }
      }
    }
    if (digitalRead(btn_up) == LOW ){
      if (select_menu_washer == 0){
        if (main_menu_washer == 1){
          lcd.clear();
          delay(150);
          main_menu_washer = 2;
        }
      }
      else if (select_menu_washer == 1){
        if (sub_menu_washer == 1){
          menit_washer++;
          if (menit_washer > 59) menit_washer = 0;
        }
        else if (sub_menu_washer == 2){
          detik_washer++;
          if (detik_washer > 59) detik_washer = 0;
        }
        else if (sub_menu_washer == 3){
          millidetik_washer++;
          if (millidetik_washer > 999) millidetik_washer = 0;
        }
      }
      else if (select_menu_washer == 2){
        if (sub_menu_washer == 1){
          menit_mati_washer++;
          if (menit_mati_washer > 59) menit_mati_washer = 0;
        }
        else if (sub_menu_washer == 2){
          detik_mati_washer++;
          if (detik_mati_washer > 59) detik_mati_washer = 0;
        }
        else if (sub_menu_washer == 3){
          millidetik_mati_washer++;
          if (millidetik_mati_washer > 999) millidetik_mati_washer = 0;
        }
      }
    }
    if (digitalRead(btn_down) == LOW){
      if (select_menu_washer == 0){
        if (main_menu_washer == 2){
          lcd.clear();
          delay(150);
          main_menu_washer = 1;
        }
      }
      else if (select_menu_washer == 1){
        if (sub_menu_washer == 1){
          menit_washer--;
          if (menit_washer < 0) menit_washer = 59;
        } 
        else if (sub_menu_washer == 2){
          detik_washer--;
          if (detik_washer < 0) detik_washer = 59;
        }
        else if (sub_menu_washer == 3){
          millidetik_washer--;
          if (millidetik_washer < 0) millidetik_washer = 999;
        }
      }
      else if (select_menu_washer == 2){
        if (sub_menu_washer == 1){
          menit_mati_washer--;
          if (menit_mati_washer < 0) menit_mati_washer = 59;
        }
        else if (sub_menu_washer == 2){
          detik_mati_washer--;
          if (detik_mati_washer < 0) detik_mati_washer = 59;
        }
        else if (sub_menu_washer == 3){
          millidetik_mati_washer--;
          if (millidetik_mati_washer < 0) millidetik_mati_washer = 999;
        }
      }
    }
  }
}

void display(){
  if (tampilan == "utama") {
    display_flow_rate();
  }
  if (tampilan == "washer"){
    display_washer();
    read_EEPROM();
    stopwatch_washer();
  }
  if (tampilan == "setting"){
    if (select_setting == 0){
      if (sub_setting == 0){
      lcd.setCursor(7, 0);
      lcd.print("SETTING");

      if (menu_setting == 1){
        lcd.setCursor(0, 1);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Flow Rate");
        lcd.setCursor(15, 1);
        lcd.write(6);
        lcd.print(status_flow);
        lcd.write(5);
        lcd.setCursor(2, 2);
        lcd.print("Spin LT");
        lcd.setCursor(15, 2);
        lcd.write(6);
        lcd.print(status_spin);
        lcd.write(5);
        lcd.setCursor(2, 3);
        lcd.print("Washer LT");
        lcd. setCursor(15, 3);
        lcd.write(6);
        lcd.print(status_washer);
        lcd.write(5);
      }
      else if (menu_setting == 2){
        lcd.setCursor(0, 2);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Flow Rate");
        lcd. setCursor(15, 1);
        lcd.write(6);
        lcd.print(status_flow);
        lcd.write(5);
        lcd.setCursor(2, 2);
        lcd.print("Spin LT");
        lcd. setCursor(15, 2);
        lcd.write(6);
        lcd.print(status_spin);
        lcd.write(5);
        lcd.setCursor(2, 3);
        lcd.print("Washer LT");
        lcd. setCursor(15, 3);
        lcd.write(6);
        lcd.print(status_washer);
        lcd.write(5);
      }
      else if (menu_setting == 3){
        lcd.setCursor(0, 3);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Flow Rate");
        lcd. setCursor(15, 1);
        lcd.write(6);
        lcd.print(status_flow);
        lcd.write(5);
        lcd.setCursor(2, 2);
        lcd.print("Spin LT");
        lcd. setCursor(15, 2);
        lcd.write(6);
        lcd.print(status_spin);
        lcd.write(5);
        lcd.setCursor(2, 3);
        lcd.print("Washer LT");
        lcd. setCursor(15, 3);
        lcd.write(6);
        lcd.print(status_washer);
        lcd.write(5);
       }
     }
    }

    else if (select_setting == 1){
      lcd.setCursor(7, 0);
      lcd.print("SETTING");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_setting == 1){
        lcd.setCursor(2, 1);
        lcd.print("Flow Rate");
        if (blink_state == 0) {
        lcd. setCursor(15, 1);
        lcd.write(6);
        if (status_flow == true) {
        lcd.print("ON ");
        } else {
        lcd.print("OFF");
        }
        lcd.write(5);
        } else {
          lcd.setCursor(15, 1);
          lcd.print("     ");
        }
        lcd.setCursor(2, 2);
        lcd.print("Spin LT");
        lcd. setCursor(15, 2);
        lcd.write(6);
        lcd.print(status_spin);
        lcd.write(5);
        lcd.setCursor(2, 3);
        lcd.print("Washer LT");
        lcd. setCursor(15, 3);
        lcd.write(6);
        lcd.print(status_washer);
        lcd.write(5);
      }    
    }
    else if (select_setting == 2){
      lcd.setCursor(7, 0);
      lcd.print("SETTING");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_setting == 1){
        lcd.setCursor(2, 1);
        lcd.print("Flow Rate");
        lcd. setCursor(15, 1);
        lcd.write(6);
        lcd.print(status_flow);
        lcd.write(5);
        lcd.setCursor(2, 2);
        lcd.print("Spin LT");
        if (blink_state == 0) {
        lcd. setCursor(15, 2);
        lcd.write(6);
        if (status_spin == true) {
        lcd.print("ON");
        } else {
        lcd.print("OFF");
        }
        lcd.write(5);
        } else {
          lcd.setCursor(15, 2);
          lcd.print("     ");
        }
        lcd.setCursor(2, 3);
        lcd.print("Washer LT");
        lcd. setCursor(15, 3);
        lcd.write(6);
        lcd.print(status_washer);
        lcd.write(5);
      }    
    }
    else if (select_setting == 3){
      lcd.setCursor(7, 0);
      lcd.print("SETTING");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_setting == 1){
        lcd.setCursor(2, 1);
        lcd.print("Flow Rate");
        lcd. setCursor(15, 1);
        lcd.write(6);
        lcd.print(status_flow);
        lcd.write(5);
        lcd.setCursor(2, 2);
        lcd.print("Spin LT");
        lcd. setCursor(15, 2);
        lcd.write(6);
        lcd.print(status_spin);
        lcd.write(5);
        lcd.setCursor(2, 3);
        lcd.print("Washer LT");
        if (blink_state == 0) {
        lcd. setCursor(15, 3);
        lcd.write(6);
        if (status_washer == true) {
        lcd.print("ON");
        } else {
        lcd.print("OFF");
        }
        lcd.write(5);
        } else {
          lcd.setCursor(15, 3);
          lcd.print("     ");
        }
      }    
    }
  }
  if (tampilan == "life"){
    if(sub_page == 0){
    display_life_test();
    read_EEPROM();
    status_indikator();
    stopwatch();
    }

    else if (sub_page == 1){
      stopwatch();
      lcd.setCursor(8, 0);
      lcd.print("Actual");
      lcd.setCursor(5, 2);
      lcd.print("Counter");
      lcd.print(":");
      display_position(hasil);
    }
  }
  if (tampilan == "mode") {
    if (select_mode == 0) {
      lcd.setCursor(4, 0);
      lcd.print("PILIHAN MODE");

      if (sub_mode == 1) {
        lcd.setCursor(0, 1);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Stop");
        lcd.setCursor(10, 1);
        lcd.print("Start");
        lcd.setCursor(2, 2);
        lcd.print("Flow Rate");
        lcd.setCursor(2, 3);
        lcd.print("Spin Life Test");
      }
      else if (sub_mode == 2) {
        lcd.setCursor(9, 1);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Stop");
        lcd.setCursor(10, 1);
        lcd.print("Start");
        lcd.setCursor(2, 2);
        lcd.print("Flow Rate");
        lcd.setCursor(2, 3);
        lcd.print("Spin Life Test");
      }
      else if (sub_mode == 3) {
        lcd.setCursor(0, 2);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Stop");
        lcd.setCursor(10, 1);
        lcd.print("Start");
        lcd.setCursor(2, 2);
        lcd.print("Flow Rate");
        lcd.setCursor(2, 3);
        lcd.print("Spin Life Test");
      }

      else if (sub_mode == 4) {
        lcd.setCursor(0, 3);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Stop");
        lcd.setCursor(10, 1);
        lcd.print("Start");
        lcd.setCursor(2, 2);
        lcd.print("Flow Rate");
        lcd.setCursor(2, 3);
        lcd.print("Spin Life Test");
      }
      else if (sub_mode == 5) {
        lcd.setCursor(0, 1);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Washer Life Test");
        lcd.setCursor(2, 2);
        lcd.print("Setting");
      }
      else if (sub_mode == 6) {
        lcd.setCursor(0, 2);
        lcd.write(5);
        lcd.setCursor(2, 1);
        lcd.print("Washer Life Test");
        lcd.setCursor(2, 2);
        lcd.print("Setting");
      }
    }
  }
  if (tampilan == "menu"){
    if (select_menu == 0){
    lcd.setCursor(8, 0);
    lcd.print("MENU");
      if (sub_menu == 0){
        if (main_menu == 1){
          lcd.setCursor(5, 2);
          lcd.print("Set  Timer");
          lcd.setCursor(19, 2);
          lcd.write(5);
        }
        if (main_menu == 2){
          lcd.setCursor(4, 2);
          lcd.print("Set  Counter");
          lcd.setCursor(19, 2);
          lcd.write(5);
          lcd.setCursor(0, 2);
          lcd.write(6);
        }
        if ( main_menu == 3){
          lcd.setCursor(8, 2);
          lcd.print("Exit");
          lcd.setCursor(0, 2);
          lcd.write(6);
        }
      }
    }
    else if (select_menu == 1) {
      lcd.setCursor(5, 0);
      lcd.print("Set  Waktu");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_menu == 1) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu ON");
        if (blink_state == 0) {
          lcd.setCursor(10, 2);
          display_position(menit_target);
        } else {
          lcd.setCursor(10, 2);
          lcd.print("  ");
        } 
        lcd.print(":");
        display_position(detik_target);
      }
      else if (sub_menu == 2) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu ON");
        lcd.setCursor(10, 2);
        display_position(menit_target);
        lcd.print(":");
        if (blink_state == 0) {
          lcd.setCursor(13, 2);
          display_position(detik_target);
        } else {
          lcd.setCursor(13, 2);
          lcd.print("  ");
        }
      }
    }

    else if (select_menu == 4) {
      lcd.setCursor(5, 0);
      lcd.print("Set  Waktu");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_menu == 1) {
        lcd.setCursor(0, 2);
        lcd.print("Waktu OFF");
        if (blink_state == 0) {
          lcd.setCursor(10, 2);
          display_position(menit_mati_target);
        } else {
          lcd.setCursor(10, 2);
          lcd.print("  ");
        }
        lcd.print(":");
        display_position(detik_mati_target);
      }
      else if (sub_menu == 2) {
        lcd.setCursor(0, 2);
        lcd.print("Waktu OFF");
        lcd.setCursor(10, 2);
        display_position(menit_mati_target);
        lcd.print(":");
        if (blink_state == 0) {
          lcd.setCursor(13, 2);
          display_position(detik_mati_target);
        } else {
          lcd.setCursor(13, 2);
          lcd.print("  ");
        }
      }
    }

    else if (select_menu == 2) {
      lcd.setCursor(4, 0);
      lcd.print("Set  Counter");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_menu == 2) {
        lcd.setCursor(0, 2);
        lcd.print("Counter:");
        if (blink_state == 0) {
          lcd.setCursor(9, 2);
          display_position(counter_target);
        } else {
          lcd.setCursor(9, 2);
          lcd.print("      ");
        }
      }
    }
  }
  if (tampilan == "menu_washer"){
    if (select_menu_washer == 0){
    lcd.setCursor(8, 0);
    lcd.print("MENU");
      if (sub_menu_washer == 0){
        if (main_menu_washer == 1){
          lcd.setCursor(5, 2);
          lcd.print("Set  Timer");
          lcd.setCursor(19, 2);
          lcd.write(5);
        }
        if ( main_menu_washer == 2){
          lcd.setCursor(8, 2);
          lcd.print("Exit");
          lcd.setCursor(0, 2);
          lcd.write(6);
        }
      }
    }
    else if (select_menu_washer == 1) {
      lcd.setCursor(5, 0);
      lcd.print("Set  Waktu");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_menu_washer == 1) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu ON");
        if (blink_state == 0) {
          lcd.setCursor(10, 2);
          display_position(menit_washer);
        } else {
          lcd.setCursor(10, 2);
          lcd.print("  ");
        } 
        lcd.print(":");
        display_position(detik_washer);
        lcd.print(".");
        display_position(millidetik_washer);
      }
      else if (sub_menu_washer == 2) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu ON");
        lcd.setCursor(10, 2);
        display_position(menit_washer);
        lcd.print(":");
        if (blink_state == 0) {
          lcd.setCursor(13, 2);
          display_position(detik_washer);
        } else {
          lcd.setCursor(13, 2);
          lcd.print("  ");
        }
        lcd.print(".");
        display_position(millidetik_washer);
      }
      else if (sub_menu_washer == 3) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu ON");
        lcd.setCursor(10, 2);
        display_position(menit_washer);
        lcd.print(":");
        lcd.setCursor(13, 2);
        display_position(detik_washer);
        lcd.print(".");
        if (blink_state == 0) {
          lcd.setCursor(16, 2);
          display_position(millidetik_washer);
        } else {
          lcd.setCursor(16, 2);
          lcd.print("  ");
        }
      }
    }

    else if (select_menu_washer == 2) {
      lcd.setCursor(5, 0);
      lcd.print("Set  Waktu");

      if (digitalRead(btn_up) == LOW or digitalRead(btn_down) == LOW) {
      blink_state = 0;
      }
      else {
        blink_text(250);
      }
      
      if (sub_menu_washer == 1) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu OFF");
        if (blink_state == 0) {
          lcd.setCursor(11, 2);
          display_position(menit_mati_washer);
        } else {
          lcd.setCursor(11, 2);
          lcd.print("  ");
        } 
        lcd.print(":");
        display_position(detik_mati_washer);
        lcd.print(".");
        display_position(millidetik_mati_washer);
      }
      else if (sub_menu_washer == 2) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu OFF");
        lcd.setCursor(11, 2);
        display_position(menit_mati_washer);
        lcd.print(":");
        if (blink_state == 0) {
          lcd.setCursor(14, 2);
          display_position(detik_mati_washer);
        } else {
          lcd.setCursor(14, 2);
          lcd.print("  ");
        }
        lcd.print(".");
        display_position(millidetik_mati_washer);
      }
      else if (sub_menu_washer == 3) {
        lcd.setCursor(1, 2);
        lcd.print("Waktu OFF");
        lcd.setCursor(11, 2);
        display_position(menit_mati_washer);
        lcd.print(":");
        lcd.setCursor(14, 2);
        display_position(detik_mati_washer);
        lcd.print(".");
        if (blink_state == 0) {
          lcd.setCursor(17, 2);
          display_position(millidetik_mati_washer);
        } else {
          lcd.setCursor(17, 2);
          lcd.print("  ");
        }
      }
    }
  }
}

void display_flow_rate(){ 
  nilai = analogRead(pot);
  analogWrite(12, nilai);
  Serial.println("Pot  : " + String(nilai));
  lcd.setCursor(3,0);
  lcd.print("Flow Rate:");
  lcd.setCursor(3,1);
  lcd.print(String(peta(nilai,0,4095,60,0)));
  lcd.setCursor(9, 1);
  lcd.print("L/m");
  delay(250);

}

float peta(float val, float min1, float max1, float min2, float max2){
  float y;
  y = (((max2-min2)/(max1-min1))*(val-min1)) + min2;
  return y;
}

void display_life_test(){
  lcd.setCursor(0, 0);
  lcd.print("No");
  lcd.setCursor(4, 0);
  lcd.print("Set");
  lcd.setCursor(12, 0);
  lcd.print("Target");

  lcd.setCursor(1, 1);
  lcd.print("1");
  lcd.setCursor(3, 1);
  lcd.print("Counter");
  lcd.setCursor(12, 1);
  lcd.print(counter_target);

  lcd.setCursor(1, 2);
  lcd.print("2");
  lcd.setCursor(3, 2);
  lcd.print("On Timer");
  lcd.setCursor(12, 2);
  lcd.print(menit_target);
  lcd.print(":");
  lcd.print(detik_target);

  lcd.setCursor(1, 3);
  lcd.print("3");
  lcd.setCursor(3, 3);
  lcd.print("OffTimer");
  lcd.setCursor(12, 3);
  lcd.print(menit_mati_target);
  lcd.print(":");
  lcd.print(detik_mati_target);

}

void display_washer(){
  lcd.setCursor(0, 0);
  lcd.print("No");
  lcd.setCursor(4, 0);
  lcd.print("Set");
  lcd.setCursor(12, 0);
  lcd.print("Target");

  lcd.setCursor(1, 1);
  lcd.print("1");
  lcd.setCursor(3, 1);
  lcd.print("On Timer");
  lcd.setCursor(12, 1);
  lcd.print(menit_washer);
  lcd.print(":");
  lcd.print(detik_washer);
  lcd.print(".");
  lcd.print(millidetik_washer);

  lcd.setCursor(1, 2);
  lcd.print("2");
  lcd.setCursor(3, 2);
  lcd.print("OffTimer");
  lcd.setCursor(12, 2);
  lcd.print(menit_mati_washer);
  lcd.print(":");
  lcd.print(detik_mati_washer);
  lcd.print(".");
  lcd.print(millidetik_mati_washer);
}

void stopwatch(){
    selesai = millis();

  // MATH time!!!
    dataStopWatch = selesai - mulai;
    dataStopWatch = dataPaus + dataStopWatch;

    menit = int(dataStopWatch / 60000);
    over = dataStopWatch % 60000;
    detik = int(over / 1000);
    over = over % 1000;

    if (i == 1 && fPaus == 0) {
      if (menit == menit_target && detik == detik_target){
        i = !i;
         fPaus = 2;
         digitalWrite(relay, HIGH);
         mulaiStopwatchKedua = millis();
    }
    Serial.println(menit, 0);
    Serial.print(".");
    if (detik <= 9) {
      Serial.print("0");
    }
    Serial.println(detik, 0);
    Serial.print("   ");

    }

    lastmenit = menit;
    lastdetik = detik;

     if (fPaus == 2 && i == 0) {
      selesai1 = millis();
      dataStopWatch1 = selesai1 - mulaiStopwatchKedua;

    menit1 = int(dataStopWatch1 / 60000);
    over1 = dataStopWatch1 % 60000;
    detik1 = int(over1 / 1000);
    over1 = over1 % 1000;

      if (menit1 == menit_mati_target && detik1 == detik_mati_target){
        i = 0; // Set ulang ke 0 agar stopwatch pertama dimulai dari awal
      fPaus = 0;
      dataPaus = 0;
        hasil++;
        EEPROM.write(15 , (hasil >> 24) & 0xFF);
        EEPROM.write(16, (hasil >> 16) & 0xFF);
        EEPROM.write(17, (hasil >> 8) & 0xFF);
        EEPROM.write(18, hasil & 0xFF);
        EEPROM.commit();
        delay(50);
        digitalWrite(relay, LOW);
        mulai = millis();

        i = 1;
      fPaus = 0;
      mulai = millis();
  }
    Serial.println(menit1, 0);
    Serial.print(".");
    if (detik1 <= 9) {
      Serial.print("0");
    }
    Serial.println(detik1, 0);
    Serial.print("   ");

    lastmenit1 = menit1;
    lastdetik1 = detik1;

    }
  }


void stopwatch_washer(){
    selesai = millis();

  // MATH time!!!
    dataStopWatch = selesai - mulai;
    dataStopWatch = dataPaus + dataStopWatch;

    menit = int(dataStopWatch / 60000);
    over = dataStopWatch % 60000;
    detik = int(over / 1000);
    over = over % 1000;
    millidetik = int(over / 10);
    over = over % 10;

    if (i == 1 && fPaus == 0) {
      if (menit == menit_washer && detik == detik_washer){
        i = !i;
         fPaus = 2;
         if (siklus == 0){
         digitalWrite(relay1, HIGH);
         mulaiStopwatchKedua = millis();
         }
         else if (siklus == 1){
         digitalWrite(relay2, HIGH);
         mulaiStopwatchKedua = millis();
         }
    }
    Serial.println(menit, 0);
    Serial.print(":");
    if (detik <= 9) {
      Serial.print("0");
    }
    Serial.println(detik, 0);
    Serial.print(".");
    if (millidetik <= 9) {
      Serial.print("0");
    }
    Serial.println(millidetik, 0);
    Serial.print("   ");

    }

    lastmenit = menit;
    lastdetik = detik;
    lastmillidetik = millidetik;

     if (fPaus == 2 && i == 0) {
      selesai1 = millis();
      dataStopWatch1 = selesai1 - mulaiStopwatchKedua;

    menit1 = int(dataStopWatch1 / 60000);
    over1 = dataStopWatch1 % 60000;
    detik1 = int(over1 / 1000);
    over1 = over1 % 1000;
    millidetik1 = int(over1 / 10);
    over1 = over1 % 10;

      if (menit1 == menit_mati_washer && detik1 == detik_mati_washer){
        i = 0; // Set ulang ke 0 agar stopwatch pertama dimulai dari awal
      fPaus = 0;
      dataPaus = 0;
        hasil = count++;
       if (siklus == 0){
         digitalWrite(relay1, LOW);
         mulai = millis();
         siklus = 1;
         }
         else if (siklus == 1){
         digitalWrite(relay2, LOW);
         mulai = millis();
         siklus = 0;
         }
      
        i = 1;
      fPaus = 0;
      mulai = millis();
      }
    Serial.println(menit1, 0);
    Serial.print(":");
    if (detik1 <= 9) {
      Serial.print("0");
    }
    Serial.println(detik1, 0);
    Serial.print(".");
    if (millidetik1 <= 9) {
      Serial.print("0");
    }
    Serial.println(millidetik1, 0);
    Serial.print("   ");

    lastmenit1 = menit1;
    lastdetik1 = detik1;
    lastmillidetik1 = millidetik1;

    }
  }

void blink_text(int lama) {
  unsigned long blink_currentMillis = millis();
  if (blink_currentMillis - blink_previousMillis > lama) {
    blink_previousMillis = blink_currentMillis;
    if (blink_state == 0) {
      blink_state = 1;
    } else {
      blink_state = 0;
    }
  }
}
void status_indikator(){
  if(counter_target >= hasil){
        digitalWrite(led, LOW);
      }
  else if (hasil >= counter_target){
    digitalWrite(led, HIGH);
  }
}
void read_EEPROM(){
  menit_target = EEPROM.read(1);
  detik_target = EEPROM.read(2);
  menit_mati_target = EEPROM.read(3);
  detik_mati_target = EEPROM.read(4);
  counter_target = (EEPROM.read(5 ) << 24) +
                   (EEPROM.read(6 ) << 16) +
                   (EEPROM.read(7 ) << 8 ) +
                    EEPROM.read(8 );
  menit_washer = EEPROM.read(9);
  detik_washer = EEPROM.read(10);
  millidetik_washer = EEPROM.read(11);
  menit_mati_washer = EEPROM.read(12);
  detik_mati_washer = EEPROM.read(13);
  millidetik_mati_washer = EEPROM.read(14);
  hasil = (EEPROM.read(15 ) << 24) +
          (EEPROM.read(16 ) << 16) +
          (EEPROM.read(17 ) << 8 ) +
          EEPROM.read(18 );
  status_flow = EEPROM.read(19);
  status_spin = EEPROM.read(20);
  status_washer = EEPROM.read(21);
  delay(50);
}
void display_position(int digits) {
  if (digits < 10)
    lcd.print("0");
  lcd.print(digits);
}
