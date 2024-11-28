---
layout: post
title: "TicTacGrow: From Bunny to Dragon"
date: 2024-11-23 16:00:00 -0400
categories: Games
---

# TicTacGrow: From Bunny to Dragon

Start with a friendly bunny in Easy Mode, then challenge the dragon when you're ready! A progressive Tic Tac Toe game that grows with your skills.

<div id="tictactoe-game-root"></div>

<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<style>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
  font-family: system-ui, -apple-system, sans-serif;
}

.mode-buttons {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.mode-button {
  padding: 0.5rem 1rem;
  border-radius: 9999px;
  border: none;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.3s;
}

.mode-button.easy {
  background-color: #FED7E2;
  color: #702459;
}

.mode-button.easy.active {
  background-color: #D53F8C;
  color: white;
}

.mode-button.ultimate {
  background-color: #FED7D7;
  color: #742A2A;
}

.mode-button.ultimate.active {
  background-color: #E53E3E;
  color: white;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 0.5rem;
  background-color: #E2E8F0;
  padding: 1rem;
  border-radius: 1rem;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.game-cell {
  width: 5rem;
  height: 5rem;
  border: 4px solid #CBD5E0;
  border-radius: 0.75rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  background-color: white;
  cursor: pointer;
  transition: all 0.3s;
}

.game-cell:hover:not(:disabled) {
  transform: scale(1.05);
}

.message {
  margin: 1rem 0;
  padding: 0.5rem 1rem;
  border-radius: 9999px;
  background-color: white;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
  font-weight: bold;
}

.stats {
  display: flex;
  gap: 1rem;
}

.stat-item {
  padding: 0.5rem 1rem;
  border-radius: 9999px;
  background-color: white;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
}

.challenge-modal {
  position: fixed;
  inset: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
}

.modal-content {
  background-color: white;
  padding: 1.5rem;
  border-radius: 1rem;
  max-width: 24rem;
  text-align: center;
}

.modal-buttons {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-top: 1rem;
}

.hint-button {
  padding: 0.5rem 1rem;
  border-radius: 9999px;
  border: none;
  cursor: pointer;
  background-color: #48BB78;
  color: white;
  font-weight: bold;
  transition: all 0.3s;
}

.hint-button:hover {
  transform: scale(1.05);
  background-color: #38A169;
}
</style>

<script type="text/babel">
// {% raw %}
const TicTacToe = () => {
  const [board, setBoard] = React.useState(Array(9).fill(null));
  const [isHumanTurn, setIsHumanTurn] = React.useState(true);
  const [gameOver, setGameOver] = React.useState(false);
  const [stats, setStats] = React.useState({ easy: { wins: 0, streak: 0 }, ultimate: { wins: 0, streak: 0 }});
  const [message, setMessage] = React.useState("Let's start with Easy Mode! 🌟");
  const [showHint, setShowHint] = React.useState(false);
  const [currentHint, setCurrentHint] = React.useState(null);
  const [gameMode, setGameMode] = React.useState('easy');
  const [showChallengePrompt, setShowChallengePrompt] = React.useState(false);

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

  React.useEffect(() => {
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

  const resetGame = () => {
    setBoard(Array(9).fill(null));
    setIsHumanTurn(true);
    setGameOver(false);
    setShowHint(false);
    setCurrentHint(null);
  };

  const renderBoard = () => (
    <div className="game-board">
      {board.map((cell, i) => {
        const { line } = calculateWinner(board);
        const isWinningCell = line?.includes(i);
        const isHintCell = showHint && currentHint?.index === i;
        
        return (
          <button
            key={i}
            className="game-cell"
            style={{
              backgroundColor: isWinningCell ? 
                (gameMode === 'easy' ? '#FEF3C7' : '#FEE2E2') : 
                isHintCell ? '#D1FAE5' : 'white',
              borderColor: gameMode === 'easy' ? '#FBD38D' : '#FCA5A5'
            }}
            onClick={() => handleClick(i)}
            disabled={gameOver || !!cell}
          >
            {cell}
          </button>
        );
      })}
    </div>
  );

return (
    <div className="game-container">
      {showChallengePrompt && (
        <div className="challenge-modal">
          <div className="modal-content">
            <h2 className="text-xl font-bold text-red-600 mb-4">🏆 Amazing Progress!</h2>
            <p className="mb-4">
              Wow! You've won {stats.easy.streak} games in a row! 
              You're getting really good at this! Ready for a bigger challenge?
            </p>
            <div className="modal-buttons">
              <button
                className="mode-button ultimate"
                onClick={() => handleModeSwitch('ultimate')}
              >
                Accept Challenge 🐉
              </button>
              <button
                className="mode-button easy"
                onClick={() => setShowChallengePrompt(false)}
              >
                Stay in Easy Mode
              </button>
            </div>
          </div>
        </div>
      )}

      <div className="mode-buttons">
        <button
          className={`mode-button easy ${gameMode === 'easy' ? 'active' : ''}`}
          onClick={() => handleModeSwitch('easy')}
        >
          Easy Mode 🌟
        </button>
        <button
          className={`mode-button ultimate ${gameMode === 'ultimate' ? 'active' : ''}`}
          onClick={() => handleModeSwitch('ultimate')}
        >
          Ultimate Mode 🐉
        </button>
      </div>

      <div className="stats">
        <div className="stat-item">
          Wins: {stats[gameMode].wins} 🏆
        </div>
        {stats[gameMode].streak > 1 && (
          <div className="stat-item">
            Streak: {stats[gameMode].streak} 🔥
          </div>
        )}
      </div>

      <div className="message">
        {message}
      </div>

      <div className="game-board">
        {board.map((cell, i) => {
          const { line } = calculateWinner(board);
          const isWinningCell = line?.includes(i);
          const isHintCell = showHint && currentHint?.index === i;
          
          return (
            <button
              key={i}
              className="game-cell"
              style={{
                backgroundColor: isWinningCell ? 
                  (gameMode === 'easy' ? '#FEF3C7' : '#FEE2E2') : 
                  isHintCell ? '#D1FAE5' : 'white',
                borderColor: gameMode === 'easy' ? '#FBD38D' : '#FCA5A5'
              }}
              onClick={() => handleClick(i)}
              disabled={gameOver || !!cell}
            >
              {cell}
            </button>
          );
        })}
      </div>

      <div className="game-controls">
        <button
          className="mode-button"
          style={{
            backgroundColor: gameMode === 'easy' ? '#D53F8C' : '#E53E3E',
            color: 'white'
          }}
          onClick={resetGame}
        >
          New Game 🎮
        </button>
        
        {gameMode === 'easy' && !gameOver && (
          <button
            className="hint-button"
            onClick={() => {
              setShowHint(!showHint);
              setCurrentHint(getHint());
            }}
          >
            {showHint ? 'Hide Hint' : 'Show Hint'} 💡
          </button>
        )}
      </div>

      <div className="game-info">
        <p>You ({PLAYERS[gameMode].human}) vs {gameMode === 'easy' ? 'Friendly' : 'Ultimate'} AI ({PLAYERS[gameMode].ai})</p>
      </div>
    </div>
  );
};

// Render the game
ReactDOM.render(
  <TicTacToe />,
  document.getElementById('tictactoe-game-root')
);
// {% endraw %}
</script>
            
