# ArduinoProject
Brick Breaker Game for Arduino Uno  Experience the classic brick breaker game on Arduino Uno! This project uses an OLED display for gameplay, featuring a paddle, ball mechanics, and score tracking. Enhance your Arduino skills and have fun!

# Brick Breaker Game for Arduino Uno

Welcome to the Brick Breaker Game project for Arduino Uno! This classic game has been brought to life on the Arduino platform, utilizing an OLED display to provide an immersive gameplay experience. Navigate your paddle to break bricks and score high!

## Features

- **Dynamic Paddle Control**: Use a potentiometer to move your paddle across the screen.
- **Score System**: Earn points by breaking bricks. Try to beat your high score!
- **Life Count**: You start with three lives, be careful not to let the ball slip past the paddle!
- **Automatic Level Progression**: As you clear the screen of bricks, the game gets more challenging.

## Hardware Requirements

- Arduino Uno
- Adafruit SSD1306 OLED Display
- Potentiometer (for paddle control)
- Push buttons (for starting the game and navigating menus)
- Wires and breadboard for connections

## Software Requirements

- Arduino IDE
- Adafruit GFX Library
- Adafruit SSD1306 Library

## Setup and Installation

1. **Connect the Hardware**: Assemble your circuit according to the schematic provided in the `schematic` folder.
2. **Install Libraries**: Open Arduino IDE, go to `Sketch > Include Library > Manage Libraries...`, and install `Adafruit GFX` and `Adafruit SSD1306`.
3. **Load the Code**: Open the `BrickBreaker.ino` file in Arduino IDE, connect your Arduino Uno via USB, and upload the sketch.

## Controls

- **Move Paddle**: Turn the potentiometer to move the paddle left or right.
- **Start Game/Select Option**: Press the start button to begin the game or select an option in the menu.

## Contributing

Feel free to fork this project, make your own changes, and open a pull request with improvements. All contributions are welcome!

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
