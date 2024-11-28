---
layout: post
title: "Multi-Player Scrabble Score Tracker - Part 1/2"
date: 2024-11-28 11:42:00 -0400
categories: Games
---

# Multi-Player Scrabble Score Tracker

A dynamic Scrabble board that tracks scores and handles letter multipliers automatically! (Part 1 of 2)

<div id="scrabble-game-root"></div>

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

.game-board {
  display: grid;
  grid-template-columns: repeat(15, 1fr);
  gap: 1px;
  background-color: #E2E8F0;
  padding: 0.5rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.cell {
  width: 2.5rem;
  height: 2.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
  background-color: white;
  cursor: pointer;
  transition: all 0.2s;
  position: relative;
  border: 1px solid #E2E8F0;
}

.cell.TW { background-color: #FED7D7; }
.cell.DW { background-color: #FED7E2; }
.cell.TL { background-color: #BEE3F8; }
.cell.DL { background-color: #B2F5EA; }

.cell:hover:not(:disabled) {
  transform: scale(1.05);
}

.score-board {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
  margin: 1rem 0;
}

.score-card {
  padding: 0.75rem 1.5rem;
  border-radius: 0.5rem;
  background-color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  text-align: center;
  min-width: 100px;
}

.setup-container {
  max-width: 24rem;
  margin: 2rem auto;
  padding: 1rem;
}

.player-list {
  margin: 1rem 0;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.player-item {
  padding: 0.5rem;
  background-color: #f3f4f6;
  border-radius: 0.25rem;
}

.button {
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  border: none;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s;
  margin: 0 0.25rem;
}

.button.primary {
  background-color: #3b82f6;
  color: white;
}

.button.danger {
  background-color: #ef4444;
  color: white;
}

.button:hover:not(:disabled) {
  transform: scale(1.05);
}

.button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.game-controls {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

select {
  padding: 0.5rem;
  border-radius: 0.25rem;
  border: 1px solid #e2e8f0;
}

input {
  padding: 0.5rem;
  border-radius: 0.25rem;
  border: 1px solid #e2e8f0;
}

.message {
  margin: 1rem 0;
  padding: 0.5rem 1rem;
  border-radius: 9999px;
  background-color: white;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
  font-weight: bold;
  text-align: center;
}
</style>

<script type="text/babel">
// {% raw %}
const ScrabbleGame = () => {
  const BOARD_SIZE = 15;
  const [board, setBoard] = React.useState(() => {
    const saved = localStorage.getItem('scrabbleBoard');
    return saved ? JSON.parse(saved) : Array(BOARD_SIZE).fill().map(() => 
      Array(BOARD_SIZE).fill({ letter: '', player: '', isNew: false })
    );
  });
  
  const [players, setPlayers] = React.useState(() => {
    const saved = localStorage.getItem('scrabblePlayers');
    return saved ? JSON.parse(saved) : [];
  });
  
  const [scores, setScores] = React.useState(() => {
    const saved = localStorage.getItem('scrabbleScores');
    return saved ? JSON.parse(saved) : {};
  });
  
  const [selectedPlayer, setSelectedPlayer] = React.useState('');
  const [selectedPosition, setSelectedPosition] = React.useState(null);
  const [turnLetters, setTurnLetters] = React.useState([]);
  const [isSettingUp, setIsSettingUp] = React.useState(players.length === 0);
  const [newPlayerName, setNewPlayerName] = React.useState('');
  const [message, setMessage] = React.useState("Start by adding players! 🎮");

  const letterScores = {
    a: 1, b: 3, c: 3, d: 2, e: 1, f: 4, g: 2, h: 4, i: 1, j: 8,
    k: 5, l: 1, m: 3, n: 1, o: 1, p: 3, q: 10, r: 1, s: 1, t: 1,
    u: 1, v: 4, w: 4, x: 8, y: 4, z: 10
  };

  const specialSquares = {
    DL: [[3,0], [11,0], [6,2], [8,2], [0,3], [7,3], [14,3], [2,6], [6,6], [8,6], [12,6], [3,7], [11,7], [2,8], [6,8], [8,8], [12,8], [0,11], [7,11], [14,11], [6,12], [8,12], [3,14], [11,14]],
    TL: [[5,1], [9,1], [1,5], [5,5], [9,5], [13,5], [1,9], [5,9], [9,9], [13,9], [5,13], [9,13]],
    DW: [[1,1], [13,1], [2,2], [12,2], [3,3], [11,3], [4,4], [10,4], [4,10], [10,10], [3,11], [11,11], [2,12], [12,12], [1,13], [13,13]],
    TW: [[0,0], [7,0], [14,0], [0,7], [14,7], [0,14], [7,14], [14,14]]
  };

  React.useEffect(() => {
    localStorage.setItem('scrabbleBoard', JSON.stringify(board));
    localStorage.setItem('scrabblePlayers', JSON.stringify(players));
    localStorage.setItem('scrabbleScores', JSON.stringify(scores));
  }, [board, players, scores]);
<script type="text/babel">
// {% raw %}

  const getSquareType = (row, col) => {
    if (specialSquares.TW.some(([r, c]) => r === row && c === col)) return 'TW';
    if (specialSquares.DW.some(([r, c]) => r === row && c === col)) return 'DW';
    if (specialSquares.TL.some(([r, c]) => r === row && c === col)) return 'TL';
    if (specialSquares.DL.some(([r, c]) => r === row && c === col)) return 'DL';
    return '';
  };

  const getSquareLabel = (type) => {
    switch(type) {
      case 'TW': return 'TW';
      case 'DW': return 'DW';
      case 'TL': return 'TL';
      case 'DL': return 'DL';
      default: return '';
    }
  };

  const handleCellClick = (row, col) => {
    setSelectedPosition({ row, col });
  };

  const handleKeyDown = (e) => {
    if (!selectedPosition) return;
    
    const { row, col } = selectedPosition;
    
    if (e.key === 'Backspace' || e.key === 'Delete') {
      if (board[row][col].isNew) {
        const newBoard = [...board];
        newBoard[row][col] = { letter: '', player: '', isNew: false };
        setBoard(newBoard);
        setTurnLetters(turnLetters.filter(l => !(l.row === row && l.col === col)));
        setMessage(`Letter removed by ${selectedPlayer} 🔄`);
      }
      return;
    }
    
    if (e.key.length === 1 && e.key.match(/[a-z]/i)) {
      const newBoard = [...board];
      newBoard[row][col] = { 
        letter: e.key.toLowerCase(), 
        player: selectedPlayer,
        isNew: true 
      };
      setBoard(newBoard);
      setTurnLetters([...turnLetters, { row, col, letter: e.key.toLowerCase() }]);
      setMessage(`${selectedPlayer} placed ${e.key.toUpperCase()} ✨`);

      if (selectedPosition.col < BOARD_SIZE - 1) {
        setSelectedPosition({ row, col: col + 1 });
      }
    }
  };

  const findWords = () => {
    let words = [];
    let visited = new Set();

    const getVisitedKey = (row, col) => `${row},${col}`;
    
    turnLetters.forEach(({ row, col }) => {
      // Check horizontal word
      let word = { positions: [] };
      let c = col;
      while (c >= 0 && board[row][c].letter) {
        word.positions.unshift({ row, col: c, letter: board[row][c].letter });
        c--;
      }
      c = col + 1;
      while (c < BOARD_SIZE && board[row][c].letter) {
        word.positions.push({ row, col: c, letter: board[row][c].letter });
        c++;
      }
      if (word.positions.length > 1) {
        const key = word.positions.map(p => getVisitedKey(p.row, p.col)).join('|');
        if (!visited.has(key)) {
          words.push(word);
          visited.add(key);
        }
      }

      // Check vertical word
      word = { positions: [] };
      let r = row;
      while (r >= 0 && board[r][col].letter) {
        word.positions.unshift({ row: r, col, letter: board[r][col].letter });
        r--;
      }
      r = row + 1;
      while (r < BOARD_SIZE && board[r][col].letter) {
        word.positions.push({ row: r, col, letter: board[r][col].letter });
        r++;
      }
      if (word.positions.length > 1) {
        const key = word.positions.map(p => getVisitedKey(p.row, p.col)).join('|');
        if (!visited.has(key)) {
          words.push(word);
          visited.add(key);
        }
      }
    });

    return words;
  };

  const calculateScore = () => {
    const usedMultipliers = new Set();
    let totalScore = 0;
    let words = findWords();
    
    words.forEach(word => {
      let wordScore = 0;
      let wordMultiplier = 1;
      
      word.positions.forEach(({ row, col, letter }) => {
        let letterScore = letterScores[letter.toLowerCase()] || 0;
        const squareType = getSquareType(row, col);
        const posKey = `${row},${col}`;
        
        if (board[row][col].isNew && !usedMultipliers.has(posKey)) {
          if (squareType === 'DL') letterScore *= 2;
          if (squareType === 'TL') letterScore *= 3;
          if (squareType === 'DW') wordMultiplier *= 2;
          if (squareType === 'TW') wordMultiplier *= 3;
          usedMultipliers.add(posKey);
        }
        
        wordScore += letterScore;
      });
      
      totalScore += wordScore * wordMultiplier;
    });

    return totalScore;
  };

  const handleAddPlayer = (e) => {
    e.preventDefault();
    if (newPlayerName.trim()) {
      const updatedPlayers = [...players, newPlayerName.trim()];
      setPlayers(updatedPlayers);
      setScores(prev => ({ ...prev, [newPlayerName.trim()]: 0 }));
      setNewPlayerName('');
      if (!selectedPlayer) setSelectedPlayer(newPlayerName.trim());
      setMessage(`${newPlayerName.trim()} joined the game! 🎉`);
    }
  };

  const startGame = () => {
    if (players.length >= 2) {
      setIsSettingUp(false);
      setMessage("Game started! Click any square and type to place letters 🎲");
    }
  };

  const submitTurn = () => {
    const score = calculateScore();
    setScores(prev => ({
      ...prev,
      [selectedPlayer]: prev[selectedPlayer] + score
    }));

    const newBoard = board.map(row =>
      row.map(cell => ({ ...cell, isNew: false }))
    );
    setBoard(newBoard);
    setTurnLetters([]);
    setMessage(`${selectedPlayer} scored ${score} points! 🎯`);
  };

  const resetGame = () => {
    if (window.confirm('Start a new game? This will clear the current board.')) {
      setBoard(Array(BOARD_SIZE).fill().map(() => 
        Array(BOARD_SIZE).fill({ letter: '', player: '', isNew: false })
      ));
      setPlayers([]);
      setScores({});
      setSelectedPlayer('');
      setTurnLetters([]);
      setIsSettingUp(true);
      setMessage("Starting fresh! Add players to begin 🎮");
      localStorage.removeItem('scrabbleBoard');
      localStorage.removeItem('scrabblePlayers');
      localStorage.removeItem('scrabbleScores');
    }
  };

  if (isSettingUp) {
    return (
      <div className="setup-container">
        <h2 className="text-xl font-bold mb-4">New Scrabble Game</h2>
        <form onSubmit={handleAddPlayer} className="space-y-4">
          <div className="flex gap-2">
            <input
              type="text"
              value={newPlayerName}
              onChange={(e) => setNewPlayerName(e.target.value)}
              placeholder="Player name"
              className="flex-1 p-2 border rounded"
            />
            <button
              type="submit"
              disabled={!newPlayerName}
              className="button primary"
            >
              Add Player
            </button>
          </div>

          <div className="player-list">
            {players.map((player, i) => (
              <div key={i} className="player-item">{player}</div>
            ))}
          </div>

          <button
            type="button"
            onClick={startGame}
            disabled={players.length < 2}
            className="button primary w-full"
          >
            Start Game ({players.length < 2 ? `Need ${2 - players.length} more` : 'Ready!'})
          </button>
        </form>
      </div>
    );
  }

  return (
    <div className="game-container">
      <div className="game-controls">
        <select
          value={selectedPlayer}
          onChange={(e) => setSelectedPlayer(e.target.value)}
          className="p-2 border rounded"
        >
          {players.map((player, i) => (
            <option key={i} value={player}>{player}</option>
          ))}
        </select>
        <button
          onClick={submitTurn}
          disabled={turnLetters.length === 0}
          className="button primary"
        >
          Submit Turn
        </button>
        <button
          onClick={resetGame}
          className="button danger"
        >
          New Game
        </button>
      </div>

      <div className="message">{message}</div>

      <div className="score-board">
        {players.map((player, i) => (
          <div key={i} className="score-card">
            <div className="font-bold">{player}</div>
            <div className="text-2xl">{scores[player]}</div>
          </div>
        ))}
      </div>

      <div className="game-board" tabIndex={0} onKeyDown={handleKeyDown}>
        {board.map((row, rowIndex) => (
          row.map((cell, colIndex) => {
            const squareType = getSquareType(rowIndex, colIndex);
            const isSelected = selectedPosition?.row === rowIndex && selectedPosition?.col === colIndex;
            return (
              <div
                key={`${rowIndex}-${colIndex}`}
                className={`
                  cell ${squareType}
                  ${isSelected ? 'ring-2 ring-blue-500' : ''}
                  ${cell.isNew ? 'font-bold' : ''}
                `}
                onClick={() => handleCellClick(rowIndex, colIndex)}
              >
                {cell.letter ? (
                  <span className="text-lg">{cell.letter.toUpperCase()}</span>
                ) : (
                  <span className="text-xs text-gray-500">{getSquareLabel(squareType)}</span>
                )}
              </div>
            );
          })
        ))}
      </div>
    </div>
  );
};

ReactDOM.render(
  <ScrabbleGame />,
  document.getElementById('scrabble-game-root')
);
// {% endraw %}
</script>
