---
layout: post
title: "Crocodile Apple Game"
date: 2024-11-23 14:00:00 -0400
categories: Games
---

# Crocodile Apple Game

A simple game where you control a crocodile trying to eat apples. Use the arrow keys to move!

<div id="crocodile-game-root"></div>

<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<script type="text/babel">
const CrocodileAppleGame = () => {
  const [crocodilePos, setCrocodilePos] = React.useState({ x: 50, y: 50 });
  const [applePos, setApplePos] = React.useState({ x: 200, y: 200 });
  const [isSnapping, setIsSnapping] = React.useState(false);
  const [jawAngle, setJawAngle] = React.useState(0);
  const [direction, setDirection] = React.useState('right');
  const [score, setScore] = React.useState(0);
  
  React.useEffect(() => {
    const moveSpeed = 8;
    
    const handleKeyPress = (e) => {
      const newPos = { ...crocodilePos };
      let newDirection = direction;
      
      switch(e.key) {
        case 'ArrowUp':
          newPos.y = Math.max(40, newPos.y - moveSpeed);
          break;
        case 'ArrowDown':
          newPos.y = Math.min(240, newPos.y + moveSpeed);
          break;
        case 'ArrowLeft':
          newPos.x = Math.max(40, newPos.x - moveSpeed);
          newDirection = 'left';
          break;
        case 'ArrowRight':
          newPos.x = Math.min(340, newPos.x + moveSpeed);
          newDirection = 'right';
          break;
        default:
          return;
      }
      
      setCrocodilePos(newPos);
      setDirection(newDirection);
    };
    
    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, [crocodilePos, direction]);
  
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
            const newX = Math.floor(Math.random() * 280) + 60;
            const newY = Math.floor(Math.random() * 180) + 60;
            setApplePos({ x: newX, y: newY });
          }
        }, 75);
      }
    };
    
    checkCollision();
  }, [crocodilePos, applePos, isSnapping]);

  return (
    <div style={{ display: 'flex', flexDirection: 'column', alignItems: 'center', gap: '1rem' }}>
      <div style={{ fontSize: '1.5rem', fontWeight: 'bold', color: '#276749' }}>Score: {score}</div>
      <div style={{ position: 'relative', width: '384px', height: '320px', borderRadius: '0.5rem', overflow: 'hidden', border: '4px solid #276749' }}>
        {/* Background */}
        <div style={{ 
          position: 'absolute', 
          inset: 0, 
          background: 'linear-gradient(180deg, #C6F6D5 0%, #9AE6B4 100%)'
        }}>
          <svg width="100%" height="100%" style={{ opacity: 0.2 }}>
            <pattern id="grass" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
              <path d="M0,20 L10,0 L20,20" fill="none" stroke="darkgreen" strokeWidth="1"/>
              <path d="M-10,20 L0,0 L10,20" fill="none" stroke="darkgreen" strokeWidth="1"/>
              <path d="M10,20 L20,0 L30,20" fill="none" stroke="darkgreen" strokeWidth="1"/>
            </pattern>
            <rect width="100%" height="100%" fill="url(#grass)"/>
          </svg>
        </div>

        {/* Apple */}
        <svg 
          style={{ 
            position: 'absolute',
            left: applePos.x - 20,
            top: applePos.y - 20,
            filter: 'drop-shadow(3px 3px 3px rgba(0,0,0,0.3))',
            transition: 'all 0.2s'
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

        {/* Crocodile */}
        <svg 
          style={{ 
            position: 'absolute',
            left: crocodilePos.x - 50,
            top: crocodilePos.y - 30,
            transform: `scaleX(${direction === 'left' ? -1 : 1})`,
            filter: 'drop-shadow(4px 4px 4px rgba(0,0,0,0.3))',
            transition: 'all 0.1s'
          }}
          width="100" 
          height="60" 
          viewBox="0 0 100 60"
        >
          {/* Body */}
          <path d="M20,30 Q50,20 80,30 Q90,30 96,24 Q90,36 80,36 Q50,46 20,36 Q10,36 4,30 Q10,24 20,30" 
            fill="#2f855a" stroke="#1a4731" strokeWidth="2"/>
          
          {/* Lower Jaw */}
          <path d="M80,30 Q90,30 96,24 L100,26 L96,28 L100,30 L96,32 L100,34 L96,36" 
            fill="#2f855a" stroke="#1a4731" strokeWidth="2"/>
          
          {/* Upper Jaw */}
          <g transform={`rotate(${-jawAngle} 80 30)`}>
            <path d="M80,30 Q90,30 96,24 L100,22 L96,24 L100,26 L96,28 L100,30"
              fill="#2f855a" stroke="#1a4731" strokeWidth="2"/>
            {/* Teeth */}
            <path d="M84,29 L86,26 L88,29 L90,26 L92,29 L94,26 L96,29" 
              fill="white" stroke="#1a4731" strokeWidth="1"/>
          </g>
          
          {/* Eye */}
          <circle cx="84" cy="28" r="3" fill="#1a4731"/>
          <circle cx="84" cy="28" r="1" fill="white"/>
          
          {/* Back spikes */}
          <path d="M30,24 L34,16 L40,22 L46,14 L52,20 L58,12 L64,18 L70,10 L76,16" 
            stroke="#1a4731" strokeWidth="2" fill="none"/>
          
          {/* Legs */}
          <path d="M30,36 Q34,44 40,36" stroke="#1a4731" strokeWidth="2" fill="none"/>
          <path d="M60,36 Q64,44 70,36" stroke="#1a4731" strokeWidth="2" fill="none"/>
          
          {/* Scales */}
          <path d="M30,32 Q34,30 38,32 M40,32 Q44,30 48,32 M50,32 Q54,30 58,32 M60,32 Q64,30 68,32"
            stroke="#1a4731" strokeWidth="1" fill="none"/>
        </svg>

        {/* Instructions */}
        <div style={{ 
          position: 'absolute', 
          bottom: '0.5rem', 
          left: '0.5rem',
          fontSize: '0.875rem',
          color: '#276749',
          backgroundColor: 'rgba(255,255,255,0.5)',
          padding: '0 0.5rem',
          borderRadius: '0.25rem'
        }}>
          Use arrow keys to move the crocodile
        </div>
      </div>
    </div>
  );
};

// Render the game
ReactDOM.render(
  <CrocodileAppleGame />,
  document.getElementById('crocodile-game-root')
);
</script>
