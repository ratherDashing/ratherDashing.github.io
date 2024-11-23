---
layout: post
title: "Tic Tac Grow"
date: 2024-11-23 16:00:00 -0400
categories: Games
---

# Ultimate vs Easy Tic Tac Toe

A dual-mode Tic Tac Toe game where you can play in Easy Mode (against a friendly bunny 🐰) or challenge yourself in Ultimate Mode (against a fierce dragon 🐉). Perfect for both beginners and experts!

## Game Component

```jsx
iimport React, { useState, useEffect } from 'react';

const TicTacToe = () => {
  const [board, setBoard] = useState(Array(9).fill(null));
  const [isHumanTurn, setIsHumanTurn] = useState(true);
  const [gameOver, setGameOver] = useState(false);
  const [stats, setStats] = useState({ easy: { wins: 0, streak: 0 }, ultimate: { wins: 0, streak: 0 }});
  const [message, setMessage] = useState("Let's start with Easy Mode! 🌟");
  const [showHint, setShowHint] = useState(false);
  const [currentHint, setCurrentHint] = useState(null);
  const [gameMode, setGameMode] = useState('easy');
  const [showChallengePrompt, setShowChallengePrompt] = useState(false);

  const CHALLENGE_THRESHOLD = 3;
  
  const PLAYERS = {
    easy: { human: '🌟', ai: '🐰' },
    ultimate: { human: '⚔️', ai: '🐉' }
  };

  const calculateWinner = (squares) => {
    const lines = [
      [0, 1, 2], [3, 4, 5], [6, 7, 8],
      [0, 3, 6], [1, 4, 7], [2, 5, 8],
      [0, 4, 8], [2, 4, 6]
    ];
    
    for (const [a, b, c] of lines) {
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
        return { winner: squares[a], line: [a, b, c] };
      }
    }
    return { winner: null, line: null };
  };

  const findWinningMove = (board, player) => {
    for (let i = 0; i < 9; i++) {
      if (!board[i]) {
        const testBoard = [...board];
        testBoard[i] = player;
        if (calculateWinner(testBoard).winner === player) {
          return i;
        }
      }
    }
    return null;
  };

  const findAIMove = () => {
    if (gameMode === 'easy') {
      // Easy mode: 70% random moves
      if (Math.random() < 0.7) {
        const emptySpots = board
          .map((cell, index) => cell === null ? index : null)
          .filter(cell => cell !== null);
        
        if (emptySpots.length > 0) {
          return emptySpots[Math.floor(Math.random() * emptySpots.length)];
        }
      }
      
      // Block only 50% of the time in easy mode
      const blockingMove = findWinningMove(board, PLAYERS.easy.human);
      if (blockingMove !== null && Math.random() < 0.5) {
        return blockingMove;
      }
    } else {
      // Ultimate mode: Try to win first
      const winningMove = findWinningMove(board, PLAYERS.ultimate.ai);
      if (winningMove !== null) return winningMove;

      // Then block
      const blockingMove = findWinningMove(board, PLAYERS.ultimate.human);
      if (blockingMove !== null) return blockingMove;

      // Take center if available
      if (board[4] === null) return 4;

      // Take corners
      const corners = [0, 2, 6, 8];
      const emptyCorners = corners.filter(i => board[i] === null);
      if (emptyCorners.length > 0) {
        return emptyCorners[Math.floor(Math.random() * emptyCorners.length)];
      }
    }

    // Default to any available move
    const emptySpots = board
      .map((cell, index) => cell === null ? index : null)
      .filter(cell => cell !== null);
    return emptySpots.length > 0 ? emptySpots[Math.floor(Math.random() * emptySpots.length)] : null;
  };

  const getHint = () => {
    if (gameMode === 'ultimate') return null;

    const winningMove = findWinningMove(board, PLAYERS.easy.human);
    if (winningMove !== null) {
      return { index: winningMove, message: "Win the game here! 🎯" };
    }

    if (board[4] === null) {
      return { index: 4, message: "The center is a great move! ⭐" };
    }

    const corners = [0, 2, 6, 8].filter(i => board[i] === null);
    if (corners.length > 0) {
      return { 
        index: corners[Math.floor(Math.random() * corners.length)],
        message: "Corner squares are strong moves! 📐" 
      };
    }

    const emptySpots = board
      .map((cell, index) => cell === null ? index : null)
      .filter(cell => cell !== null);
    
    if (emptySpots.length > 0) {
      return { 
        index: emptySpots[0],
        message: "Try this spot! ✨" 
      };
    }

    return null;
  };

  useEffect(() => {
    if (!isHumanTurn && !gameOver) {
      const timer = setTimeout(() => {
        const aiMove = findAIMove();
        if (aiMove !== null) {
          const newBoard = [...board];
          newBoard[aiMove] = PLAYERS[gameMode].ai;
          setBoard(newBoard);
          setIsHumanTurn(true);
          
          const { winner } = calculateWinner(newBoard);
          if (winner || newBoard.every(cell => cell !== null)) {
            setGameOver(true);
            const newStats = {
              ...stats,
              [gameMode]: {
                wins: winner === PLAYERS[gameMode].human ? stats[gameMode].wins + 1 : stats[gameMode].wins,
                streak: winner === PLAYERS[gameMode].human ? stats[gameMode].streak + 1 : 0
              }
            };
            setStats(newStats);

            if (winner === PLAYERS[gameMode].human) {
              setMessage("🎉 Amazing victory! You're getting better!");
              if (newStats.easy.streak >= CHALLENGE_THRESHOLD && !showChallengePrompt && gameMode === 'easy') {
                setShowChallengePrompt(true);
              }
            } else if (winner === PLAYERS[gameMode].ai) {
              setMessage(gameMode === 'easy' ? 
                "Good try! Want to play again? 🎮" : 
                "The dragon prevails! Try again? 🐉");
            } else {
              setMessage("It's a tie! Well played! 🤝");
            }
          }
        }
      }, gameMode === 'easy' ? 1000 : 300);
      return () => clearTimeout(timer);
    }
  }, [isHumanTurn, gameOver, board]);

  const handleClick = (i) => {
    if (!isHumanTurn || board[i] || gameOver) return;

    const newBoard = [...board];
    newBoard[i] = PLAYERS[gameMode].human;
    setBoard(newBoard);
    setIsHumanTurn(false);
    setShowHint(false);

    const { winner } = calculateWinner(newBoard);
    if (winner || newBoard.every(cell => cell !== null)) {
      setGameOver(true);
      if (winner === PLAYERS[gameMode].human) {
        const newStats = {
          ...stats,
          [gameMode]: {
            wins: stats[gameMode].wins + 1,
            streak: stats[gameMode].streak + 1
          }
        };
        setStats(newStats);
        if (newStats.easy.streak >= CHALLENGE_THRESHOLD && !showChallengePrompt && gameMode === 'easy') {
          setShowChallengePrompt(true);
        }
      }
    } else {
      setMessage(gameMode === 'easy' ? 
        "Bunny is thinking... 🐰" : 
        "Dragon is planning... 🐉");
    }
  };

  const handleModeSwitch = (mode) => {
    setGameMode(mode);
    setShowChallengePrompt(false);
    resetGame();
    setMessage(mode === 'easy' ? 
      "Welcome to Easy Mode! Have fun! 🌟" : 
      "Welcome to Ultimate Mode! Face the dragon! 🐉");
  };

  const renderSquare = (i) => {
    const { line } = calculateWinner(board);
    const isWinningSquare = line?.includes(i);
    const isHintSquare = showHint && currentHint?.index === i;
    
    return (
      <button
        className={`w-24 h-24 border-4 
          ${gameMode === 'easy' ? 'border-pink-300' : 'border-red-400'}
          text-5xl font-bold 
          ${!board[i] && !gameOver ? 'hover:bg-gray-100' : 'cursor-not-allowed'}
          ${isWinningSquare ? (gameMode === 'easy' ? 'bg-yellow-100' : 'bg-red-100') : 
            isHintSquare ? 'bg-green-100' : 'bg-white'}
          rounded-xl transition-all duration-300 ease-in-out
          transform hover:scale-105
          focus:outline-none shadow-lg`}
        onClick={() => handleClick(i)}
        disabled={gameOver || !!board[i]}
      >
        {board[i]}
      </button>
    );
  };

  const resetGame = () => {
    setBoard(Array(9).fill(null));
    setIsHumanTurn(true);
    setGameOver(false);
    setShowHint(false);
    setCurrentHint(null);
  };

  return (
    <div className={`flex flex-col items-center gap-4 p-6 
      ${gameMode === 'easy' ? 'bg-gradient-to-b from-pink-50 to-purple-50' 
        : 'bg-gradient-to-b from-red-50 to-gray-100'} 
      min-h-96 rounded-lg relative`}>
      
      {showChallengePrompt && (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
          <div className="bg-white p-6 rounded-xl shadow-2xl max-w-md m-4">
            <h2 className="text-2xl font-bold text-red-600 mb-4">🏆 Amazing Progress!</h2>
            <p className="mb-4">
              Wow! You've won {stats.easy.streak} games in a row! 
              You're getting really good at this! Ready for a bigger challenge?
            </p>
            <div className="flex gap-4 justify-center">
              <button
                className="px-6 py-3 bg-red-500 text-white rounded-full hover:bg-red-600 transform hover:scale-105 transition shadow-lg"
                onClick={() => handleModeSwitch('ultimate')}
              >
                Accept Challenge 🐉
              </button>
              <button
                className="px-6 py-3 bg-gray-500 text-white rounded-full hover:bg-gray-600 transition"
                onClick={() => setShowChallengePrompt(false)}
              >
                Stay in Easy Mode
              </button>
            </div>
          </div>
        </div>
      )}

      <h1 className={`text-3xl font-bold mb-2 
        ${gameMode === 'easy' ? 'text-pink-600' : 'text-red-600'}`}>
        {gameMode === 'easy' ? 'Fun & Easy' : 'Ultimate Challenge'} Mode
      </h1>

      <div className="flex gap-4 mb-4">
        <button
          className={`px-6 py-2 rounded-full transition 
            ${gameMode === 'easy' ? 'bg-pink-500 text-white' : 'bg-gray-200'}`}
          onClick={() => handleModeSwitch('easy')}
        >
          Easy Mode 🌟
        </button>
        <button
          className={`px-6 py-2 rounded-full transition 
            ${gameMode === 'ultimate' ? 'bg-red-500 text-white' : 'bg-gray-200'}`}
          onClick={() => handleModeSwitch('ultimate')}
        >
          Ultimate Mode 🐉
        </button>
      </div>

      <div className="flex gap-4 mb-4">
        <div className="bg-white px-4 py-2 rounded-full shadow-md">
          Wins: {stats[gameMode].wins} 🏆
        </div>
        {stats[gameMode].streak > 1 && (
          <div className="bg-yellow-100 px-4 py-2 rounded-full shadow-md">
            Streak: {stats[gameMode].streak} 🔥
          </div>
        )}
      </div>

      <div className="text-xl mb-4 h-12 text-center px-6 py-2 bg-white rounded-full shadow-md">
        {message}
      </div>

      <div className="grid grid-cols-3 gap-2 bg-gray-200 p-4 rounded-xl shadow-xl">
        {[0,1,2,3,4,5,6,7,8].map(i => renderSquare(i))}
      </div>

      <div className="flex gap-4 mt-4">
        <button
          className={`px-6 py-3 rounded-full 
            ${gameMode === 'easy' ? 'bg-pink-500' : 'bg-red-500'} 
            text-white font-bold shadow-lg hover:scale-105 transition`}
          onClick={resetGame}
        >
          New Game 🎮
        </button>
        
        {gameMode === 'easy' && !gameOver && (
          <button
            className="px-6 py-3 bg-green-500 text-white rounded-full font-bold
              shadow-lg hover:scale-105 transition"
            onClick={() => {
              setShowHint(!showHint);
              setCurrentHint(getHint());
            }}
          >
            {showHint ? 'Hide Hint' : 'Show Hint'} 💡
          </button>
        )}
      </div>

      <div className="mt-4 text-center text-gray-600">
        <p>You ({PLAYERS[gameMode].human}) vs {gameMode === 'easy' ? 'Friendly' : 'Ultimate'} AI ({PLAYERS[gameMode].ai})</p>
      </div>
    </div>
  );
};

export default TicTacToe;
