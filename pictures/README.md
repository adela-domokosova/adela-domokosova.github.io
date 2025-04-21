# Rock-Paper-Scissors Documetation

This document defines, game rules, storage, communication protocol and workload distribution in Rock-Paper-Scissors network game.

## Game rules

### Objective

Defeat your opponent by choosing a move that beats theirs according to the game's rules.

Each player chooses from :
- **Rock**
- **Paper**
- **Scissors**

### Win Conditions

- Rock crushes Scissors
- Paper covers Rock
- Scissors cut Paper

### Game Flow

1. Both players submit their move.
2. The server compares moves.
3. Result is determined:
   - ✅ WIN: Your move beats opponent's
   - ❌ LOSE: Opponent's move beats yours
   - 🤝 DRAW: Same move
4. Server reveals result and opponents move.

- If a player disconnects or fails to respond, the round will be canceled.


## Proposed storage
```
rock-paper-scissors-network/
│── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── com.karel.rps/           # Root package
│   │   │   │   ├── client/              # Client-side code
│   │   │   │   │   ├── RPSClient.java   # Main client class
│   │   │   │   │   ├── ClientHandler.java # Handles UI & network communication
│   │   │   │   │   ├── GUI/             # (Optional) GUI-related classes
│   │   │   │   │   └── model/           # Client-side data models
│   │   │   │   ├── server/              # Server-side code
│   │   │   │   │   ├── RPSServer.java   # Main server class
│   │   │   │   │   ├── ServerHandler.java # Manages client connections
│   │   │   │   │   ├── GameSession.java # Handles game logic for each session
│   │   │   │   │   ├── model/           # Server-side game models
│   │   │   │   ├── common/              # Shared utilities & constants
│   │   │   │   │   ├── RPSMove.java     # Enum for Rock, Paper, Scissors
│   │   │   │   │   ├── Protocol.java    # Defines communication protocol
│   │   │   │   │   ├── LoggerUtil.java  # Logging utility
│   ├── test/
│   │   ├── java/
│   │   │   ├── com.karel.rps/
│   │   │   │   ├── client/              # Unit tests for client
│   │   │   │   ├── server/              # Unit tests for server
│   │   │   │   ├── common/              # Tests for shared utilities
│── .gitignore
│── pom.xml
│── README.md
```
## Communication protocol specification
Object serialization will be used.

| From → To       | Message Type     | Purpose                                                  | Payload                                                                 | Expected Response       |
|------------------|------------------|-----------------------------------------------------------|-------------------------------------------------------------------------|-------------------------|
| Client ⇄ Server  | PING             | Keep the connection alive                                 | None                                                                    | PING (echoed)           |
| Client → Server  | LOGIN            | Attempt login with credentials                            | `username`: String (unique)<br>`password`: Hash                         | LOGIN_RESPONSE          |
| Server → Client  | LOGIN_RESPONSE   | Inform client about login result                          | `success`: Boolean<br>`message`: String (user-friendly description)     | —                       |
| Server → Client  | GAME_START       | Notify client that a match was found and game begins      | `opponentUsername`: String                                              | —                       |
| Client → Server  | GAME_MOVE        | Submit player's move for the round                        | `move`: Enum (ROCK, PAPER, SCISSORS)                                    | ROUND_RESULT            |
| Server → Client  | ROUND_RESULT     | Send round outcome and both players' moves                | `yourMove`: Enum<br>`opponentMove`: Enum<br>`result`: WIN/LOSE/DRAW     | —                       |
| Server → Client  | DISCONNECT       | Notify client that connection is being terminated         | `reason`: String (e.g. "Inactivity", "Server shutdown")                 | —                       |
| Client → Server  | TERMINATE        | Inform server client is disconnecting voluntarily         | Optional                                                                | —                       |
| Client ⇄ Server  | ERROR            | Communicate protocol violation or unexpected issue        | `error`: String                                                         | — or ERROR (if replied) |
## Work Distribution
### Adéla – Client Side

Adéla is responsible for:

- Implementing all logic under the client package:
- Designing and building the user interface using JavaFX:
- Integrating UI and network communication

### Karel – Server Side

Karel is responsible for:

- Implementing all logic under the server package:
- Managing game sessions, client connections, and game state

### Shared Responsibility – Common Package

Both Adéla and Karel may contribute to the common package as needed:

## Figma
![login](https://raw.githubusercontent.com/adela-domokosova/adela-domokosova.github.io/refs/heads/main/pictures/RPS/Login.png)
![waiting for opponent](https://raw.githubusercontent.com/adela-domokosova/adela-domokosova.github.io/refs/heads/main/pictures/RPS/Waiting%20for%20second%20player.png)
![players pick](https://raw.githubusercontent.com/adela-domokosova/adela-domokosova.github.io/refs/heads/main/pictures/RPS/Game%20move.png)
![waiting for oponents pick](https://raw.githubusercontent.com/adela-domokosova/adela-domokosova.github.io/refs/heads/main/pictures/RPS/Waiting%20for%20game%20results.png)
![game results](https://raw.githubusercontent.com/adela-domokosova/adela-domokosova.github.io/refs/heads/main/pictures/RPS/Game%20results%20(1).png)
---