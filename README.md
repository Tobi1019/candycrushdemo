# Candy Crush Demo

This project is a basic implementation of a Candy Crush-style game using HTML, CSS, and JavaScript. The game features drag-and-drop interactions for candies, match checking, and score updating. This README provides an overview of the functionalities and the structure of the code.

## Table of Contents

- [Features](#features)
  - [Initialization](#initialization)
  - [Candy Crush Game Setup](#candy-crush-game-setup)
  - [Checking for Matches](#checking-for-matches)
  - [Interval Function](#interval-function)
- [Usage](#usage)
- [Installation](#installation)
- [Code Structure](#code-structure)
- [Contributing](#contributing)
- [License](#license)

## Features

### Initialization

The code starts by setting up an event listener that waits for the DOM to be fully loaded before initializing the game.

```js
document.addEventListener('DOMContentLoaded', () => {
  candyCrushGame();
});
```

### Candy Crush Game Setup

The `candyCrushGame` function sets up the Candy Crush game board, defines initial variables, and handles drag-and-drop events for candies.

```js
function candyCrushGame() {
  const grid = document.querySelector('.grid');
  const width = 8;
  const squares = [];
  const candyColors = [
    'url(images/red-candy.png)',
    'url(images/yellow-candy.png)',
    'url(images.orange-candy.png)',
    'url(images/purple-candy.png)',
    'url(images.green-candy.png)',
    'url(images/blue-candy.png)'
  ];

  // Create the board
  function createBoard() {
    for (let i = 0; i < width * width; i++) {
      const square = document.createElement('div');
      let randomColor = Math.floor(Math.random() * candyColors.length);
      square.style.backgroundImage = candyColors[randomColor];
      square.setAttribute('draggable', true);
      square.setAttribute('id', i);
      grid.appendChild(square);
      squares.push(square);

      square.addEventListener('dragstart', dragStart);
      square.addEventListener('dragend', dragEnd);
      square.addEventListener('dragover', dragOver);
      square.addEventListener('dragenter', dragEnter);
      square.addEventListener('dragleave', dragLeave);
      square.addEventListener('drop', dragDrop);
    }
  }
  createBoard();

  let colorBeingDragged;
  let colorBeingReplaced;
  let squareIdBeingDragged;
  let squareIdBeingReplaced;

  function dragStart() {
    colorBeingDragged = this.style.backgroundImage;
    squareIdBeingDragged = parseInt(this.id);
  }

  function dragOver(e) {
    e.preventDefault();
  }

  function dragEnter(e) {
    e.preventDefault();
  }

  function dragLeave() {
    this.style.backgroundImage = '';
  }

  function dragDrop() {
    colorBeingReplaced = this.style.backgroundImage;
    squareIdBeingReplaced = parseInt(this.id);
    this.style.backgroundImage = colorBeingDragged;
    squares[squareIdBeingDragged].style.backgroundImage = colorBeingReplaced;
  }

  function dragEnd() {
    // What is a valid move?
    let validMoves = [
      squareIdBeingDragged - 1,
      squareIdBeingDragged - width,
      squareIdBeingDragged + 1,
      squareIdBeingDragged + width
    ];
    let validMove = validMoves.includes(squareIdBeingReplaced);

    if (squareIdBeingReplaced && validMove) {
      squareIdBeingReplaced = null;
    } else if (squareIdBeingReplaced && !validMove) {
      squares[squareIdBeingReplaced].style.backgroundImage = colorBeingReplaced;
      squares[squareIdBeingDragged].style.backgroundImage = colorBeingDragged;
    } else squares[squareIdBeingDragged].style.backgroundImage = colorBeingDragged;
  }

  function moveIntoSquareBelow() {
    for (i = 0; i < 55; i++) {
      if (squares[i + width].style.backgroundImage === '') {
        squares[i + width].style.backgroundImage = squares[i].style.backgroundImage;
        squares[i].style.backgroundImage = '';
        const firstRow = [0, 1, 2, 3, 4, 5, 6, 7];
        const isFirstRow = firstRow.includes(i);
        if (isFirstRow && squares[i].style.backgroundImage === '') {
          let randomColor = Math.floor(Math.random() * candyColors.length);
          squares[i].style.backgroundImage = candyColors[randomColor];
        }
      }
    }
  }
}
```

### Checking for Matches

The code includes functions to check for matches in the game board. Matches can be in rows or columns of three or four candies.

```js
function checkRowForFour() {
  for (i = 0; i < 60; i++) {
    let rowOfFour = [i, i + 1, i + 2, i + 3];
    let decidedColor = squares[i].style.backgroundImage;
    const isBlank = squares[i].style.backgroundImage === '';

    if (rowOfFour.every(index => squares[index].style.backgroundImage === decidedColor && !isBlank)) {
      score += 4;
      rowOfFour.forEach(index => {
        squares[index].style.backgroundImage = '';
      });
    }
  }
}

function checkColumnForFour() {
  for (i = 0; i < 47; i++) {
    let columnOfFour = [i, i + width, i + width * 2, i + width * 3];
    let decidedColor = squares[i].style.backgroundImage;
    const isBlank = squares[i].style.backgroundImage === '';

    if (columnOfFour.every(index => squares[index].style.backgroundImage === decidedColor && !isBlank)) {
      score += 4;
      columnOfFour.forEach(index => {
        squares[index].style.backgroundImage = '';
      });
    }
  }
}

function checkRowForThree() {
  for (i = 0; i < 61; i++) {
    let rowOfThree = [i, i + 1, i + 2];
    let decidedColor = squares[i].style.backgroundImage;
    const isBlank = squares[i].style.backgroundImage === '';

    if (rowOfThree.every(index => squares[index].style.backgroundImage === decidedColor && !isBlank)) {
      score += 3;
      rowOfThree.forEach(index => {
        squares[index].style.backgroundImage = '';
      });
    }
  }
}

function checkColumnForThree() {
  for (i = 0; i < 47; i++) {
    let columnOfThree = [i, i + width, i + width * 2];
    let decidedColor = squares[i].style.backgroundImage;
    const isBlank = squares[i].style.backgroundImage === '';

    if (columnOfThree.every(index => squares[index].style.backgroundImage === decidedColor && !isBlank)) {
      score += 3;
      columnOfThree.forEach(index => {
        squares[index].style.backgroundImage = '';
      });
    }
  }
}
```

### Interval Function

The `setInterval` function is used to repeatedly check for matches and move candies down every 100 milliseconds.

```js
window.setInterval(function () {
  moveIntoSquareBelow();
  checkRowForFour();
  checkColumnForFour();
  checkRowForThree();
  checkColumnForThree();
}, 100);
```

## Usage

1. Clone the repository.
2. Open `index.html` in your web browser to play the game.
3. Drag and drop candies to create matches and score points.

## Installation

No special installation steps are required. Ensure you have a modern web browser to view the HTML, CSS, and JavaScript content.

## Code Structure

- **index.html**: The main HTML file containing the structure of the webpage.
- **style.css**: The CSS file for styling the webpage.
- **script.js**: The JavaScript file for game functionality and interactions.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request with your changes.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.# candycrushdemo
