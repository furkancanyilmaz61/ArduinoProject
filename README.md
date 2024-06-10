#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET    -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);



int seven_seg[] = {4, 5, 8, 7, 6, 3, 2, 9};


int num_array[10][7] = {{ 1, 1, 1, 1, 1, 1, 0 },  
                        { 0, 1, 1, 0, 0, 0, 0 },  
                        { 1, 1, 0, 1, 1, 0, 1 },  
                        { 1, 1, 1, 1, 0, 0, 1 },  
                        { 0, 1, 1, 0, 0, 1, 1 },  
                        { 1, 0, 1, 1, 0, 1, 1 },  
                        { 1, 0, 1, 1, 1, 1, 1 },  
                        { 1, 1, 1, 0, 0, 0, 0 },  
                        { 1, 1, 1, 1, 1, 1, 1 },  
                        { 1, 1, 1, 1, 0, 1, 1 }}; 


const int potPin = A0; 

const int buttonPin = A3; 
const int upButtonPin = A1; 
const int downButtonPin = A2; 

const int Led1 = 13; 
const int Led2 = 12;
const int Led3 = 11;

int menuSelection = 0; 

const int paddleWidth = 30;
const int paddleHeight = 4;
const int paddleSpeed = 3;
int paddlePosition = SCREEN_WIDTH / 2 - paddleWidth / 2;

const int ballSize = 3;
int ballPositionX = SCREEN_WIDTH / 2;
int ballPositionY = SCREEN_HEIGHT - paddleHeight - ballSize - 1;
int ballDirectionX = 1;
int ballDirectionY = -1;
int ballSpeed = 2; 

const int brickWidth = 16;
const int brickHeight = 8;
const int brickRows = 4;
const int brickCols = 8;
bool bricks[brickRows][brickCols];
const int brickPadding = 2;
int brickCount = brickRows * brickCols;

int score = 0;
int lives = 3;
bool gameStarted = false;

void setup() {
  
  for (int i = 0; i <= 8; i++) {
    pinMode(seven_seg[i], OUTPUT);
  }
  pinMode(Led1, OUTPUT);
  pinMode(Led2, OUTPUT);
  pinMode(Led3, OUTPUT);
  
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(upButtonPin, INPUT_PULLUP);
  pinMode(downButtonPin, INPUT_PULLUP);

  randomSeed(analogRead(0));

  
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.display();
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.setTextSize(2);
  display.setCursor(0, 0);
  display.println(F("> OYNA"));
  display.println(F("  CIKIS"));
  display.display();

}

void loop() {
  int buttonState = digitalRead(buttonPin);
  int upButtonState = digitalRead(upButtonPin);
  int downButtonState = digitalRead(downButtonPin);

  if (!gameStarted) {
    
    if (buttonState == LOW) {
      if (menuSelection == 0) {
        gameStarted = true;
        startGame();
      } else if (menuSelection == 1) {
        exitGame();
      }
    } else if (upButtonState == LOW) {
      
      menuSelection = (menuSelection - 1 + 2) % 2;
      updateMenuDisplay();
      delay(200); 
    } else if (downButtonState == LOW) {
      
      menuSelection = (menuSelection + 1) % 2;
      updateMenuDisplay();
      delay(200); 
    }
  } else {
    
    display.clearDisplay();
    displayPaddle();
    displayBall();
    displayBricks();
    displayLives();
    display.display();

    movePaddle();
    moveBall();
    checkCollisions();

    if (brickCount == 0) {
      levelComplete();
    }

    if (lives == 0) {
      gameOver();
    }

    delay(20);
  }
}



void updateMenuDisplay() {
  display.clearDisplay();
  display.setCursor(0, 0);
  display.setTextSize(2);
  if (menuSelection == 0) {
    display.println(F("> OYNA"));
    display.println(F("  CIKIS"));
  } else {
    display.println(F("  OYNA"));
    display.println(F("> CIKIS"));
  }
  display.display();
}


void startGame() {
  for (int row = 0; row < brickRows; ++row) {
    for (int col = 0; col < brickCols; ++col) {
      bricks[row][col] = true;
    }
  }
}


void displayPaddle() {
    display.fillRect(paddlePosition, SCREEN_HEIGHT - paddleHeight, paddleWidth, paddleHeight, SSD1306_WHITE);
}


void displayBall() {
    display.fillCircle(ballPositionX, ballPositionY, ballSize, SSD1306_WHITE);
}


void displayBricks() {
  for (int row = 0; row < brickRows; ++row) {
    for (int col = 0; col < brickCols; ++col) {
      if (bricks[row][col]) {
        int brickX = col * (brickWidth + brickPadding);
        int brickY = row * (brickHeight + brickPadding);
        display.fillRect(brickX, brickY, brickWidth, brickHeight, SSD1306_WHITE);
      }
    }
  }
}


void displayLives() {

    
    const int ledPins[3] = {Led1, Led2, Led3};

    
    for (int i = 0; i < lives; ++i) {
        digitalWrite(ledPins[i], HIGH);
    }

    
    for (int i = lives; i < 3; ++i) {
        digitalWrite(ledPins[i], LOW);
    }

    
    sev_disp(score % 10); 
}


void movePaddle() {
    int potValue = analogRead(potPin);
    paddlePosition = map(potValue, 0, 1023, 0, SCREEN_WIDTH - paddleWidth);
}


void moveBall() {
  ballPositionX += ballDirectionX * ballSpeed;
  ballPositionY += ballDirectionY * ballSpeed;
}


void checkCollisions() {
    
    if (ballPositionX <= 0 || ballPositionX >= SCREEN_WIDTH - ballSize) {
        ballDirectionX *= -1; 
    }
    
    if (ballPositionY <= 0) {
        ballDirectionY *= -1; 
    }
    
    if (ballPositionY >= SCREEN_HEIGHT - ballSize - paddleHeight) {
        if (ballPositionX >= paddlePosition && ballPositionX <= paddlePosition + paddleWidth) {
            ballDirectionY *= -1; 
        } else {
            
            ballPositionX = SCREEN_WIDTH / 2;
            ballPositionY = SCREEN_HEIGHT - paddleHeight - ballSize - 1;
            ballDirectionX = 1;
            ballDirectionY = -1;
            lives--; 
        }
    }
    
    for (int row = 0; row < brickRows; ++row) {
        for (int col = 0; col < brickCols; ++col) {
            if (bricks[row][col]) {
                int brickX = col * (brickWidth + brickPadding);
                int brickY = row * (brickHeight + brickPadding);
                
                if (ballPositionX + ballSize >= brickX && ballPositionX <= brickX + brickWidth &&
                    ballPositionY + ballSize >= brickY && ballPositionY <= brickY + brickHeight) {
                    bricks[row][col] = false; 
                    brickCount--; 
                    score++; 
                    ballDirectionY *= -1; 
                }
            }
        }
    }
}


void levelComplete() {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(10, SCREEN_HEIGHT / 2 - 8);
    display.println(F("Seviye Tamamlandi!"));
    display.display();
    delay(5000);
    
    ballPositionX = SCREEN_WIDTH / 2;
    ballPositionY = SCREEN_HEIGHT - paddleHeight - ballSize - 1;
    brickCount = brickRows * brickCols; 
    score = 0; 
    ballSpeed *= 1.2; 
    startGame(); 
}


void gameOver() {
    displayLives(); 
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(10, SCREEN_HEIGHT / 2 - 8);
    display.print(F("Oyun Bitti"));
    display.setCursor(10, SCREEN_HEIGHT / 2 + 8);
    display.println(F("Puaniniz: "));
    display.setCursor(200, SCREEN_HEIGHT / 2 + 8);
    display.println(score);
    display.display();
    delay(3000);
    gameStarted = false; 
    lives = 3; 
    score = 0; 
    brickCount = brickRows * brickCols; 
    sev_disp(score % 10); 
    updateMenuDisplay(); 
}


void exitGame() {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 0);
    display.print(F("Nesnelerin Interneti dersinde beni dinlediginiz icin tesekkurler."));
    display.display();
    delay(3000);
    updateMenuDisplay(); 
}

void sev_disp(int rakam) {
  int pin;
  for (int j = 0; j < 7; j++) {
    pin = seven_seg[j];
    digitalWrite(pin, num_array[rakam][j]); 
  }
}
