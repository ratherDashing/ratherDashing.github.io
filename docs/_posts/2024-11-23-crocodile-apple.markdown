---
layout: post
title: "Crocodile Apple Game"
date: 2024-11-23 16:00:00 -0400
categories: Games
---

# Crocodile Apple Game

A simple game where you control a crocodile trying to eat apples. Use arrow keys on desktop or touch the directional buttons on mobile!

<!-- Ensure proper mobile rendering -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<div id="crocodile-game-root"></div>

<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<style>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  touch-action: none;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  user-select: none;
}

.score {
  font-size: 1.5rem;
  font-weight: bold;
  color: #276749;
}

.game-area {
  position: relative;
  width: 384px;
  height: 320px;
  border-radius: 0.5rem;
  overflow: hidden;
  border: 4px solid #276749;
}

/* Mobile responsive game area */
@media (max-width: 480px) {
  .game-area {
    width: 320px;
    height: 260px;
  }
}

.game-background {
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, #C6F6D5 0%, #9AE6B4 100%);
}

.game-sprite {
  position: absolute;
  transition: all 0.1s;
}

.apple {
  transition: all 0.2s;
}

.instructions {
  position: absolute;
  bottom: 0.5rem;
  left: 0.5rem;
  font-size: 0.875rem;
  color: #276749;
  background-color: rgba(255, 255, 255, 0.5);
  padding: 0 0.5rem;
  border-radius: 0.25rem;
}

/* Mobile controls */
.mobile-controls {
  display: none;
  grid-template-areas:
    ". up ."
    "left . right"
    ". down .";
  gap: 0.5rem;
  margin-top: 1rem;
}

@media (hover: none) and (pointer: coarse) {
  .mobile-controls {
    display: grid;
  }
  .instructions {
    display: none;
  }
}

.control-button {
  width: 50px;
  height: 50px;
  border: none;
  border-radius: 25px;
  background-color: #276749;
  color: white;
  font-size: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  touch-action: manipulation;
  -webkit-tap-highlight-color: transparent;
  cursor: pointer;
  opacity: 0.8;
  transition: opacity 0.2s;
}

.control-button:active {
  opacity: 1;
}

.control-up { grid-area: up; }
.control-down { grid-area: down; }
.control-left { grid-area: left; }
.control-right { grid-area: right; }
</style>

<script type="text/babel">
// {% raw %}
const CrocodileAppleGame = () => {
  const [crocodilePos, setCrocodilePos] = React.useState({ x: 50, y: 50 });
  const [applePos, setApplePos] = React.useState({ x: 200, y: 200 });
  const [isSnapping, setIsSnapping] = React.useState(false);
  const [jawAngle, setJawAngle] = React.useState(0);
  const [direction, setDirection] = React.useState('right');
  const [score, setScore] = React.useState(0);
  const [isMobile, setIsMobile] = React.useState(false);
  
  // Detect mobile devices
  React.useEffect(() => {
    setIsMobile(window.matchMedia('(hover: none) and (pointer: coarse)').matches);
  }, []);
  
  const moveCharacter = React.useCallback((moveType) => {
    const moveSpeed = 8;
    const maxX = isMobile ? 280 : 340;
    const maxY = isMobile ? 200 : 240;
    
    setCrocodilePos(prev => {
      const newPos = { ...prev };
      let newDirection = direction;
      
      switch(moveType) {
        case 'up':
          newPos.y = Math.max(40, newPos.y - moveSpeed);
          break;
        case 'down':
          newPos.y = Math.min(maxY, newPos.y + moveSpeed);
          break;
        case 'left':
          newPos.x = Math.max(40, newPos.x - moveSpeed);
          newDirection = 'left';
          break;
        case 'right':
          newPos.x = Math.min(maxX, newPos.x + moveSpeed);
          newDirection = 'right';
          break;
        default:
          return prev;
      }
      
      setDirection(newDirection);
      return newPos;
    });
  }, [direction, isMobile]);
  
  // Keyboard controls
  React.useEffect(() => {
    const handleKeyPress = (e) => {
      // Prevent default scrolling behavior
      if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', ' '].includes(e.key)) {
        e.preventDefault();
      }
      
      switch(e.key) {
        case 'ArrowUp': moveCharacter('up'); break;
        case 'ArrowDown': moveCharacter('down'); break;
        case 'ArrowLeft': moveCharacter('left'); break;
        case 'ArrowRight': moveCharacter('right'); break;
      }
    };
    
    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, [moveCharacter]);
  
  React.useEffect(() => {
    const checkCollision = () => {
      const distance = Math.sqrt(
        Math.pow(crocodilePos.x - applePos.x, 2) + 
        Math.pow(crocodilePos.y - applePos.y, 2)
      );
      
      if (distance < 60 && !isSnapping) {
        setIsSnapping(true);
        setScore(s => s + 1);
        
        let frame = 0;
        const snapAnimation = setInterval(() => {
          frame++;
          if (frame <= 4) {
            setJawAngle(frame * 15);
          } else if (frame <= 12) {
            setJawAngle(Math.max(0, 60 - (frame - 4) * 7.5));
          }
          
          if (frame === 12) {
            clearInterval(snapAnimation);
            setIsSnapping(false);
            setJawAngle(0);
            const maxX = isMobile ? 240 : 280;
            const maxY = isMobile ? 140 : 180;
            const newX = Math.floor(Math.random() * maxX) + 60;
            const newY = Math.floor(Math.random() * maxY) + 60;
            setApplePos({ x: newX, y: newY });
          }
        }, 75);
      }
    };
    
    checkCollision();
  }, [crocodilePos, applePos, isSnapping, isMobile]);

  return (
    <div className="game-container">
      <div className="score">Score: {score}</div>
      <div className="game-area">
        <div className="game-background">
          <svg width="100%" height="100%" style={{ opacity: 0.2 }}>
            <pattern id="grass" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
              <path d="M0,20 L10,0 L20,20" fill="none" stroke="darkgreen" strokeWidth="1"/>
              <path d="M-10,20 L0,0 L10,20" fill="none" stroke="darkgreen" strokeWidth="1"/>
              <path d="M10,20 L20,0 L30,20" fill="none" stroke="darkgreen" strokeWidth="1"/>
            </pattern>
            <rect width="100%" height="100%" fill="url(#grass)"/>
          </svg>
        </div>

        <svg 
          className="game-sprite apple"
          style={{ 
            left: applePos.x - 20,
            top: applePos.y - 20,
            filter: 'drop-shadow(3px 3px 3px rgba(0,0,0,0.3))'
          }}
          width="40" 
          height="40" 
          viewBox="0 0 40 40"
        >
          <path d="M20,4 C24,4 31,11 31,20 C31,29 25,36 20,36 C15,36 9,29 9,20 C9,11 16,4 20,4" 
            fill="#e53e3e" stroke="#742a2a" strokeWidth="1.5"/>
          <path d="M20,4 C20,4 21,1.5 22,0" stroke="#742a2a" strokeWidth="1.5"/>
          <path d="M17,9 Q20,12 23,9" stroke="#742a2a" strokeWidth="1.5" fill="none"/>
        </svg>

        <svg 
          className="game-sprite"
          style={{ 
            left: crocodilePos.x - 50,
            top: crocodilePos.y - 30,
            transform: `scaleX(${direction === 'left' ? -1 : 1})`,
            filter: 'drop-shadow(4px 4px 4px rgba(0,0,0,0.3))',
            transition: isSnapping ? 'none' : 'all 0.1s'
          }}
          width="100" 
          height="60" 
          viewBox="0 0 100 60"
        >
          <path d="M20,30 Q50,20 80,30 Q90,30 96,24 Q90,36 80,36 Q50,46 20,36 Q10,36 4,30 Q10,24 20,30" 
            fill="#2f855a" stroke="#1a4731" strokeWidth="2"/>
          
          <path d="M80,30 Q90,30 96,24 L100,26 L96,28 L100,30 L96,32 L100,34 L96,36" 
            fill="#2f855a" stroke="#1a4731" strokeWidth="2"/>
          
          <g transform={`rotate(${-jawAngle} 80 30)`}>
            <path d="M80,30 Q90,30 96,24 L100,22 L96,24 L100,26 L96,28 L100,30"
              fill="#2f855a" stroke="#1a4731" strokeWidth="2"/>
            <path d="M84,29 L86,26 L88,29 L90,26 L92,29 L94,26 L96,29" 
              fill="white" stroke="#1a4731" strokeWidth="1"/>
          </g>
          
          <circle cx="84" cy="28" r="3" fill="#1a4731"/>
          <circle cx="84" cy="28" r="1" fill="white"/>
          
          <path d="M30,24 L34,16 L40,22 L46,14 L52,20 L58,12 L64,18 L70,10 L76,16" 
            stroke="#1a4731" strokeWidth="2" fill="none"/>
          
          <path d="M30,36 Q34,44 40,36" stroke="#1a4731" strokeWidth="2" fill="none"/>
          <path d="M60,36 Q64,44 70,36" stroke="#1a4731" strokeWidth="2" fill="none"/>
          
          <path d="M30,32 Q34,30 38,32 M40,32 Q44,30 48,32 M50,32 Q54,30 58,32 M60,32 Q64,30 68,32"
            stroke="#1a4731" strokeWidth="1" fill="none"/>
        </svg>

        <div className="instructions">
          Use arrow keys to move the crocodile
        </div>
      </div>
      
      {/* Mobile touch controls */}
      <div className="mobile-controls">
        <button 
          className="control-button control-up" 
          onTouchStart={() => moveCharacter('up')}
          aria-label="Move Up"
        >
          ↑
        </button>
        <button 
          className="control-button control-down"
          onTouchStart={() => moveCharacter('down')}
          aria-label="Move Down"
        >
          ↓
        </button>
        <button 
          className="control-button control-left"
          onTouchStart={() => moveCharacter('left')}
          aria-label="Move Left"
        >
          ←
        </button>
        <button 
          className="control-button control-right"
          onTouchStart={() => moveCharacter('right')}
          aria-label="Move Right"
        >
          →
        </button>
      </div>
    </div>
  );
};

// Render the game
ReactDOM.render(
  <CrocodileAppleGame />,
  document.getElementById('crocodile-game-root')
);
// {% endraw %}
</script>
