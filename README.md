# Wise Roll 🎲

_This project is a hybrid web application that combines JavaScript and Python to create an interactive dice game._

## Introduction

Welcome to **Wise Roll**, a two-player dice game that showcases the powerful synergy between **JavaScript** and **Python** in web development. By leveraging **PyScript**, Wise Roll seamlessly integrates Python into the frontend, harnessing the strengths of both languages to deliver an engaging and interactive gaming experience.

**Live Demo**: [Play Wise Roll on Vercel](https://wise-roll2.vercel.app/)

**Source Code**: [GitHub Repository](https://github.com/mcc1461/wise-roll)

---

## Features

- **Hybrid Application**: Combines the strengths of JavaScript and Python.
- **Interactive Gameplay**: Engaging dice game with strategic elements.
- **Responsive Design**: Seamless experience across various devices.
- **PyScript Integration**: Run Python code directly in the browser.
- **Clean and Modular Codebase**: Easy to understand and extend.
- **Live Deployment**: Hosted on Vercel for instant access.

---

## The Power of a Hybrid Approach

Combining **JavaScript** and **Python** in a single web application allows developers to harness the best features of both languages:

- **JavaScript** excels at handling dynamic user interfaces, real-time interactions, and efficient DOM manipulation.
- **Python** offers simplicity, readability, and powerful libraries, making complex logic easier to implement and maintain.

By integrating Python into the frontend with PyScript, Wise Roll demonstrates how hybrid applications can lead to more efficient and maintainable codebases, enhancing both developer productivity and user experience.

---

## Core Components

### HTML Structure

The HTML provides the foundational structure of Wise Roll, including:

- **Header**: Displays the logo and game title.
- **Main Content**: Organized into sections for rules, game info, player scores, action buttons, and dice display.
- **Footer**: Credits and additional information.
- **Modals**: For game confirmations and end-game notifications.

### Styling with CSS

CSS enhances the visual appeal and responsiveness of Wise Roll:

- **Flexbox Layout**: Creates a flexible and adaptive layout.
- **Custom Fonts and Colors**: Improves aesthetics and readability.
- **Media Queries**: Ensures responsiveness across different screen sizes.
- **Interactive Elements**: Styles buttons and hover effects for better user experience.

### Game Logic with Python

Python, powered by **PyScript**, handles the core game logic:

- **Random Dice Roll**: Utilizes Python's `random` module to simulate dice rolls.
- **Game State Management**: Keeps track of scores, turns, and game progression.
- **Handling Game Events**: Implements logic for special conditions like rolling a 1 or 4.
- **Interacting with JavaScript**: Calls JavaScript functions to update the UI dynamically.

#### Dice Rolling Function

```python
<!-- PYTHON for Logic and Game State Management -->
    <script type="py">
      from js import window
      import random

      def roll_dice():
          game_state = window.getGameState()
          current_player = game_state.currentPlayer - 1
          roll = random.randint(1, 6)
          game_state.rolls.append(str(roll))

          # Show Dice Image
          window.showDiceImage(roll)

          # Handle Roll
          if roll == 4:
              window.updateOutput("Oh no! Rolled a 4. Turn ends.")
              game_state.players[current_player].partScore = 0
              # Update Total Score
              game_state.players[current_player].totalScore = game_state.players[current_player].previousTotal
              game_state.rollCount += 1
              window.updateDisplay()
              window.disableRollButton()
              window.switchTurn()
              return
          elif roll == 1:
              game_state.players[current_player].partScore += 10
              window.updateOutput("Great! Bonus 10 points!")
          else:
              game_state.players[current_player].partScore += roll
              window.updateOutput("Keep going!")

          # Update Total Score Simultaneously
          game_state.players[current_player].totalScore = (
              game_state.players[current_player].partScore +
              game_state.players[current_player].previousTotal
          )

          # Check Roll Count
          game_state.rollCount += 1
          if game_state.rollCount >= game_state.maxRolls:
              window.updateOutput("Rolls completed for this part.")
              window.updateDisplay()
              window.disableRollButton()
              window.switchTurn()
              return

          # Update Display
          window.updateDisplay()

      # Expose roll_dice to JavaScript
      window.pyRollDice = roll_dice
    </script>
```

##### Here's how the dice rolling function works

- The roll_dice function is defined in Python.
- It accesses the game state from JavaScript using window.getGameState().
- A random number between 1 and 6 is generated to simulate a dice roll.
- Depending on the roll result:
  1. If the player rolls a 4, their turn ends, and their part score resets to 0.
  1. If the player rolls a 1, they receive 10 bonus points.
  1. For other rolls, the roll value is added to their part score.
- The function updates the game state and interacts with JavaScript functions to update the UI.

#### Interactive Elements with JavaScript

```javascript
 <!-- JAVASCRIPT for DOM Manipulation -->
    <script>
      // DOM Elements
      const turnIndicator = document.getElementById("turn-indicator");
      const partIndicator = document.getElementById("part-indicator");
      const player1Card = document.getElementById("player1-card");
      const player2Card = document.getElementById("player2-card");
      const player1Score = document.getElementById("player1-score");
      const player2Score = document.getElementById("player2-score");
      const rollButton = document.getElementById("roll-button");
      const quitButton = document.getElementById("quit-button");
      const toggleGameButton = document.getElementById("toggle-game-button");
      const diceImagesDiv = document.getElementById("dice-images");
      const rollsLine = document.getElementById("rolls-line");
      const outputMessage = document.getElementById("output-message");

      // Modal Elements
      const confirmModal = document.getElementById("confirm-modal");
      const confirmQuitButton = document.getElementById("confirm-quit");
      const cancelQuitButton = document.getElementById("cancel-quit");

      const endgameModal = document.getElementById("endgame-modal");
      const endgameMessage = document.getElementById("endgame-message");
      const restartGameButton = document.getElementById("restart-game");
      const closeEndgameButton = document.getElementById("close-endgame");

      // Game State
      const gameState = {
        currentPlayer: 1,
        part: 1,
        rollCount: 0,
        rolls: [],
        players: [
          { totalScore: 0, partScore: 0, previousTotal: 0 },
          { totalScore: 0, partScore: 0, previousTotal: 0 },
        ],
        maxRolls: 5,
        maxParts: 3,
        gameEnded: false,
      };

      // Dice Images
      const diceImages = [
        "./assets/dice-1.png",
        "./assets/dice-2.png",
        "./assets/dice-3.png",
        "./assets/dice-4.png",
        "./assets/dice-5.png",
        "./assets/dice-6.png",
      ];

      // Update Display
      function updateDisplay() {
        turnIndicator.textContent = `Player ${gameState.currentPlayer}'s Turn`;
        partIndicator.textContent = `Part ${gameState.part}`;
        player1Score.textContent = `Part: ${gameState.players[0].partScore} | Total: ${gameState.players[0].totalScore}`;
        player2Score.textContent = `Part: ${gameState.players[1].partScore} | Total: ${gameState.players[1].totalScore}`;

        // Show rolls only if there are rolls
        if (gameState.rolls.length > 0) {
          rollsLine.textContent = `Rolls: ${gameState.rolls.join(" | ")}`;
        } else {
          rollsLine.textContent = "";
        }

        // Highlight active player
        if (gameState.currentPlayer === 1) {
          player1Card.classList.add("active");
          player1Card.classList.remove("inactive");
          player2Card.classList.add("inactive");
          player2Card.classList.remove("active");
        } else {
          player2Card.classList.add("active");
          player2Card.classList.remove("inactive");
          player1Card.classList.add("inactive");
          player1Card.classList.remove("active");
        }
      }

      // Update Output Message
      function updateOutput(message) {
        outputMessage.textContent = message;
      }

      // Show Dice Image
      function showDiceImage(roll) {
        const img = document.createElement("img");
        img.src = diceImages[roll - 1];
        img.alt = `Dice ${roll}`;
        diceImagesDiv.appendChild(img);
      }

      // Clear Dice Images and Rolls
      function clearDiceDisplay() {
        diceImagesDiv.innerHTML = "";
        rollsLine.textContent = "";
      }

      // Disable Roll Button
      function disableRollButton() {
        rollButton.disabled = true;
      }

      // Enable Roll Button
      function enableRollButton() {
        rollButton.disabled = false;
      }

      // Switch Turns
      function switchTurn() {
        const current = gameState.currentPlayer - 1;
        // Store the previous total for cumulative calculation
        gameState.players[current].previousTotal =
          gameState.players[current].totalScore;
        // Reset part score
        gameState.players[current].partScore = 0;
        gameState.rollCount = 0;

        setTimeout(() => {
          // Clear dice images and rolls
          clearDiceDisplay();
          gameState.rolls = [];
          // Remove "Rolls:" text after part finished
          rollsLine.textContent = "";

          // Move to the next player or part
          if (gameState.currentPlayer === 2) {
            if (gameState.part === gameState.maxParts) {
              endGame();
              return;
            }
            gameState.part++;
          }
          gameState.currentPlayer = gameState.currentPlayer === 1 ? 2 : 1;
          // Enable Roll Button for next player
          enableRollButton();
          updateDisplay();
          outputMessage.textContent = ""; // Clear the output message for the new turn
        }, 1500); // Increased delay to 3 seconds
      }

      // End the Game
      function endGame() {
        disableRollButton();
        quitButton.disabled = true;
        const scores = gameState.players.map((p) => p.totalScore);
        const winner =
          scores[0] === scores[1]
            ? "It's a Tie!"
            : scores[0] > scores[1]
            ? "Player 1 Wins!"
            : "Player 2 Wins!";
        // Prepare the end game message with comparison
        endgameMessage.textContent = `Game Over! ${winner}\n\n${scores[0]} - ${scores[1]}`;
        // Show the endgame modal
        showEndgameModal();
        toggleGameButton.textContent = "Restart Game"; // Change button text
        gameState.gameEnded = true;
      }

      // Toggle Game (Quit or Restart)
      function toggleGame() {
        if (!gameState.gameEnded) {
          // Show custom confirmation modal
          showModal();
        } else {
          // Restart Game
          restartGame();
        }
      }

      // Show Confirmation Modal
      function showModal() {
        confirmModal.style.display = "flex";
      }

      // Hide Confirmation Modal
      function hideModal() {
        confirmModal.style.display = "none";
      }

      // Show Endgame Modal
      function showEndgameModal() {
        endgameModal.style.display = "flex";
      }

      // Hide Endgame Modal
      function hideEndgameModal() {
        endgameModal.style.display = "none";
      }

      // Restart Game
      function restartGame() {
        // Reset game state
        gameState.currentPlayer = 1;
        gameState.part = 1;
        gameState.rollCount = 0;
        gameState.rolls = [];
        gameState.players = [
          { totalScore: 0, partScore: 0, previousTotal: 0 },
          { totalScore: 0, partScore: 0, previousTotal: 0 },
        ];
        gameState.gameEnded = false;
        enableRollButton();
        quitButton.disabled = true;
        toggleGameButton.textContent = "Quit Game";
        clearDiceDisplay();
        updateOutput("");
        updateDisplay();
        hideEndgameModal();
      }

      // Confirm Quit Game
      confirmQuitButton.addEventListener("click", () => {
        // Proceed with quitting the game
        disableRollButton();
        quitButton.disabled = true;
        updateOutput("Game has been quit. Thank you for playing!");
        toggleGameButton.textContent = "Restart Game";
        gameState.gameEnded = true;
        hideModal();
      });

      // Cancel Quit Game
      cancelQuitButton.addEventListener("click", () => {
        // Do nothing, just hide the modal
        hideModal();
      });

      // Close Endgame Modal
      closeEndgameButton.addEventListener("click", () => {
        hideEndgameModal();
      });

      // Restart Game from Endgame Modal
      restartGameButton.addEventListener("click", () => {
        restartGame();
      });

      // Close modal when clicking outside the modal content
      window.addEventListener("click", (event) => {
        if (event.target == confirmModal) {
          hideModal();
        }
        if (event.target == endgameModal) {
          hideEndgameModal();
        }
      });

      // Attach Event Handlers
      rollButton.addEventListener("click", () => {
        if (!gameState.gameEnded) {
          window.pyRollDice(); // Call Python logic
          quitButton.disabled = false; // Enable Quit Part button after first roll
        }
      });

      quitButton.addEventListener("click", () => {
        updateOutput(`Part ended. Turn passes to the next player.`);
        disableRollButton(); // Disable Roll Button
        switchTurn();
      });

      toggleGameButton.addEventListener("click", () => {
        toggleGame();
      });

      // Expose State and Functions to Python
      window.getGameState = () => gameState;
      window.updateDisplay = updateDisplay;
      window.updateOutput = updateOutput;
      window.showDiceImage = showDiceImage;
      window.switchTurn = switchTurn;
      window.disableRollButton = disableRollButton;
      window.enableRollButton = enableRollButton;

      // Initial Display Update
      updateDisplay();
    </script>
```

##### Here's how the JavaScript functions interact with the game state and UI

- Event Listeners: Responds to user actions like clicks on buttons.
- DOM Manipulation: Updates scores, messages, and visual elements in real-time.
- State Management: Keeps track of the game's state and progression.
- Interfacing with Python: Bridges Python and JavaScript for a cohesive experience.

#### Exposing JavaScript Functions to Python

```python
    // Expose State and Functions to Python
window.getGameState = () => gameState;
window.updateDisplay = updateDisplay;
window.updateOutput = updateOutput;
window.showDiceImage = showDiceImage;
window.switchTurn = switchTurn;
window.disableRollButton = disableRollButton;
window.enableRollButton = enableRollButton;
```

- Global Access: Allows Python code to access and manipulate JavaScript functions and variables.
- Seamless Integration: Facilitates smooth communication between the two languages.

## Managing Python in the Frontend

Integrating Python into the frontend using PyScript opens up new possibilities for web development:

1. Installation and Setup:

   - Include PyScript's CSS and JS in the HTML `<head>` section.
   - Use `<script type="py">` tags to embed Python code directly within HTML.

1. Interoperability:

   - Utilize the window object to expose JavaScript functions to Python.
   - Call Python functions from JavaScript to handle complex logic.

1. State Management:

   - Maintain game state within JavaScript and expose it to Python for manipulation.
   - Ensure synchronization between Python logic and JavaScript-driven UI updates.

1. Advantages:

   - Python's Readability: Simplifies the implementation of complex game logic.
   - Leverage Existing Libraries: Utilize Python's extensive libraries directly in the browser.
   - Enhanced Development Speed: Rapidly prototype and iterate on game mechanics.

---

## Conclusion

**Wise Roll** exemplifies the power of a hybrid approach in web development, showcasing the seamless integration of Python and JavaScript to create an engaging and interactive dice game. By combining the strengths of both languages, developers can leverage Python's simplicity and JavaScript's interactivity to build robust and dynamic web applications.

Experience the synergy of Python and JavaScript in action with **Wise Roll** and explore the endless possibilities of hybrid web development!

---

## About the Author

This project was created by [MusCo](https://musco.dev/), a passionate developer with a love for coding, creativity, and collaboration. Connect with MusCo on [GitHub](mcc1461) and [Medium](https://medium.com/@mcc1461a) to stay updated on the latest projects and tech insights.

---

## Acknowledgements

Special thanks to the following resources and tools that made this project possible:

- **PyScript**: For enabling Python integration in the browser.

- **Vercel**: For hosting the live demo of Wise Roll.

- **Unsplash**: For providing high-quality images used in the project.

- **GitHub**: For version control and collaboration.

- **VSCode**: For a powerful development environment.

---

## License

This project is licensed under the MIT License.
