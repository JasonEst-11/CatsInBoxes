# Photographic Memory

Photographic Memory is a simple yet fun card-flipping memory game. The goal is to find and match pairs of pictures from a grid of faced-down cards. It's designed to test and improve your memory while providing an engaging experience.

The game is built using Vue.js and Bootstrap for a smooth, responsive user interface, and is hosted on Netlify.

The photos on this game are randomly generated and provided by the Lorem Picsum API. You can check out their documentation [here](https://picsum.photos/).
## Demo

You can play the game here: [Photographic Memory](https://master--photographic-memory.netlify.app/)

## Features

- **4x4 Grid**: A total of 16 cards are placed facedown in a grid layout.
- **Memory Challenge**: Flip two cards at a time to find matching pairs. If they match, they stay flipped. If not, they turn back facedown.
- **Responsive Design**: The game is fully responsive, making it playable on desktop, tablet, and mobile devices.

## Technologies Used

- **Vite**: For local development server
- **Vue.js**: For building the reactive user interface.
- **Bootstrap**: For responsive design and UI components.
- **Netlify**: For deployment and hosting.

## How to Play

1. Open the game [here](https://master--photographic-memory.netlify.app/).
2. Click on two cards to flip them over and reveal the pictures.
3. If the two cards match, they will stay face up. If not, they will flip back after a short delay.
4. The goal is to match all the cards with as few attempts as possible.

## Project Setup

To run the game locally:

1. Clone the repository:
   ```bash
   git clone https://github.com/JasonEst-11/Photographic-Memory.git
   cd Photographic-Memory
2. Install the dependencies:
   ```bash
   npm install
3. Run the project:
   ```bash
   npm run dev
