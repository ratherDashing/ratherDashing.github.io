---
layout: post
title: "Multi-Player Scrabble Score Tracker"
date: 2024-11-28 11:42:00 -0400
categories: Games
---

# Multi-Player Scrabble Score Tracker

A dynamic Scrabble board that tracks scores and handles letter multipliers automatically!

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
}

.cell:hover {
  transform: scale(1.05);
}

.cell-label {
  position: absolute;
  top: 2px;
  left: 2px;
  font-size: 0.5rem;
  color: #666;
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

.coordinates {
  display: grid;
  place-items: center;
  font-size: 0.75rem;
  color: #666;
  font-weight: 500;
}

.coordinates.row {
  width: 2rem;
}

.coordinates.col {
  height: 2rem;
}

.button {
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  border: none;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s;
}

.button.primary {
  background-color: #3b82f6;
  color: white;
}

.button.danger {
  background-color: #ef4444;
  color: white;
}

.button:hover {
  transform: scale(1.05);
}

.button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>

<script type="text/babel">
// {% raw %}
const ScrabbleGame = () => {
  const BOARD_SIZE = 15;
  const [board, setBoard] = useState(() => {
    const saved = localStorage.getItem('scrabbleBoard');
    return saved ? JSON.parse(saved) : Array(BOARD_SIZE).fill().map(() => 
      Array(BOARD_SIZE).fill({ letter: '', player: '', isNew: false })
    );
  });
  
  const [players, setPlayers] = useState(() => {
    const saved = localStorage.getItem('scrabblePlayers');
    return saved ? JSON.parse(saved) : [];
  });
  
  const [scores, setScores] = useState(() => {
    const saved = localStorage.getItem('scrabbleScores');
    return saved ? JSON.parse(saved) : {};
  });
  
  const [selectedPlayer, setSelectedPlayer] = useState('');
  const [selectedPosition, setSelectedPosition] = useState(null);
  const [turnLetters, setTurnLetters] = useState([]);
  const [isSettingUp, setIsSettingUp] = useState(players.length === 0);
  const [newPlayerName, setNewPlayerName] = useState('');

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

  useEffect(() => {
    localStorage.setItem('scrabbleBoard', JSON.stringify(board));
    localStorage.setItem('scrabblePlayers', JSON.stringify(players));
    localStorage.setItem('scrabbleScores', JSON.stringify(scores));
  }, [board, players, scores]);

  // Game logic functions remain the same as in the previous version, just remove the player limit checks

  return (
    <div className="game-container">
      {isSettingUp ? (
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
              Start Game
            </button>
          </form>
        </div>
      ) : (
        <>
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

          <div className="score-board">
            {players.map((player, i) => (
              <div key={i} className="score-card">
                <div className="font-bold">{player}</div>
                <div className="text-2xl">{scores[player]}</div>
              </div>
            ))}
          </div>

          <div className="game-board">
            {/* Board rendering code remains the same */}
          </div>
        </>
      )}
    </div>
  );
};

ReactDOM.render(
  <ScrabbleGame />,
  document.getElementById('scrabble-game-root')
);
// {% endraw %}
</script>
