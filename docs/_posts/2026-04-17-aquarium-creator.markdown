---
layout: post
title: "Aquarium Creator"
date: 2026-04-17 12:00:00 -0400
categories: Games
---

Draw your own fish and objects, then watch them come to life in your very own aquarium!

<div id="aquarium-root"></div>

<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<script type="text/babel">
// {% raw %}
const { useState, useRef, useEffect, useCallback } = React;

const AQUARIUM_W = 900;
const AQUARIUM_H = 520;
const CANVAS_SIZE = 320;

function trimCanvas(sourceCanvas) {
  const ctx = sourceCanvas.getContext("2d");
  const w = sourceCanvas.width;
  const h = sourceCanvas.height;
  const imageData = ctx.getImageData(0, 0, w, h);
  const d = imageData.data;
  let top = h, left = w, bottom = 0, right = 0;
  for (let y = 0; y < h; y++) {
    for (let x = 0; x < w; x++) {
      if (d[(y * w + x) * 4 + 3] > 0) {
        if (y < top) top = y;
        if (y > bottom) bottom = y;
        if (x < left) left = x;
        if (x > right) right = x;
      }
    }
  }
  if (top > bottom || left > right) return null;
  const pad = 2;
  const cx = Math.max(0, left - pad);
  const cy = Math.max(0, top - pad);
  const cw = Math.min(w, right - left + 1 + pad * 2);
  const ch = Math.min(h, bottom - top + 1 + pad * 2);
  const trimmed = document.createElement("canvas");
  trimmed.width = cw;
  trimmed.height = ch;
  trimmed.getContext("2d").drawImage(sourceCanvas, cx, cy, cw, ch, 0, 0, cw, ch);
  return trimmed;
}

function Fish({ data, allFish, aquariumW, aquariumH, objects }) {
  const [pos, setPos] = useState({ x: data.startX, y: data.startY });
  const [flip, setFlip] = useState(false);
  const [angle, setAngle] = useState(0);
  const velRef = useRef({
    vx: (Math.random() - 0.5) * 2,
    vy: (Math.random() - 0.5) * 1.2,
  });
  const posRef = useRef({ x: data.startX, y: data.startY });
  const wiggleRef = useRef(Math.random() * Math.PI * 2);

  useEffect(() => {
    const speed = 0.6 + Math.random() * 0.8;
    let raf;
    const update = () => {
      const v = velRef.current;
      const p = posRef.current;
      wiggleRef.current += 0.03;

      v.vx += (Math.random() - 0.5) * 0.15;
      v.vy += (Math.random() - 0.5) * 0.12;

      objects.forEach((obj) => {
        const dx = p.x - obj.x;
        const dy = p.y - obj.y;
        const dist = Math.sqrt(dx * dx + dy * dy);
        if (dist < 80) {
          v.vx += (dx / dist) * 0.4;
          v.vy += (dy / dist) * 0.4;
        }
      });

      const mag = Math.sqrt(v.vx * v.vx + v.vy * v.vy);
      if (mag > speed) {
        v.vx = (v.vx / mag) * speed;
        v.vy = (v.vy / mag) * speed;
      }

      const hw = (data.width * data.scale) / 2;
      const hh = (data.height * data.scale) / 2;
      if (p.x - hw < 0) { v.vx = Math.abs(v.vx) * 0.8 + 0.3; }
      if (p.x + hw > aquariumW) { v.vx = -Math.abs(v.vx) * 0.8 - 0.3; }
      if (p.y - hh < 10) { v.vy = Math.abs(v.vy) * 0.8 + 0.2; }
      if (p.y + hh > aquariumH - 10) { v.vy = -Math.abs(v.vy) * 0.8 - 0.2; }

      p.x += v.vx;
      p.y += v.vy;

      const wiggle = Math.sin(wiggleRef.current) * 3;
      setPos({ x: p.x, y: p.y + wiggle });
      setFlip(v.vx < 0);
      setAngle(Math.atan2(v.vy, Math.abs(v.vx)) * (180 / Math.PI) * 0.3);
      raf = requestAnimationFrame(update);
    };
    raf = requestAnimationFrame(update);
    return () => cancelAnimationFrame(raf);
  }, [data.id, aquariumW, aquariumH]);

  const sc = data.scale;
  return (
    <img
      src={data.src}
      alt="fish"
      style={{
        position: "absolute",
        left: pos.x - (data.width * sc) / 2,
        top: pos.y - (data.height * sc) / 2,
        width: data.width * sc,
        height: data.height * sc,
        transform: `scaleX(${flip ? -1 : 1}) rotate(${angle}deg)`,
        imageRendering: "pixelated",
        pointerEvents: "none",
        filter: `drop-shadow(0 2px 6px rgba(0,0,0,0.25))`,
        transition: "transform 0.15s linear",
      }}
    />
  );
}

function Bubble() {
  const [style, setStyle] = useState(null);
  useEffect(() => {
    const left = 5 + Math.random() * 90;
    const size = 4 + Math.random() * 10;
    const dur = 4 + Math.random() * 6;
    const delay = Math.random() * 8;
    setStyle({ left: `${left}%`, width: size, height: size, animationDuration: `${dur}s`, animationDelay: `${delay}s` });
  }, []);
  if (!style) return null;
  return <div className="bubble" style={style} />;
}

function Aquarium() {
  const canvasRef = useRef(null);
  const [isDrawing, setIsDrawing] = useState(false);
  const [brushSize, setBrushSize] = useState(4);
  const [brushColor, setBrushColor] = useState("#FF6B35");
  const [mode, setMode] = useState("fish");
  const [fishes, setFishes] = useState([]);
  const [objects, setObjects] = useState([]);
  const lastPt = useRef(null);
  const idCounter = useRef(0);
  const [tool, setTool] = useState("draw");

  const colors = [
    "#FF6B35", "#FFD166", "#06D6A0", "#118AB2", "#EF476F",
    "#FFFFFF", "#073B4C", "#F4A261", "#E76F51", "#2A9D8F",
    "#8338EC", "#FF006E", "#FB5607", "#3A86FF", "#FFBE0B",
    "#264653", "#E9C46A", "#000000",
  ];

  const initCanvas = useCallback(() => {
    const c = canvasRef.current;
    if (!c) return;
    const ctx = c.getContext("2d");
    ctx.clearRect(0, 0, CANVAS_SIZE, CANVAS_SIZE);
  }, []);

  useEffect(() => { initCanvas(); }, [initCanvas]);

  const getPos = (e) => {
    const c = canvasRef.current;
    const rect = c.getBoundingClientRect();
    const scaleX = CANVAS_SIZE / rect.width;
    const scaleY = CANVAS_SIZE / rect.height;
    const clientX = e.touches ? e.touches[0].clientX : e.clientX;
    const clientY = e.touches ? e.touches[0].clientY : e.clientY;
    return { x: (clientX - rect.left) * scaleX, y: (clientY - rect.top) * scaleY };
  };

  const startDraw = (e) => {
    e.preventDefault();
    setIsDrawing(true);
    const p = getPos(e);
    lastPt.current = p;
    const ctx = canvasRef.current.getContext("2d");
    ctx.beginPath();
    if (tool === "erase") {
      ctx.globalCompositeOperation = "destination-out";
      ctx.arc(p.x, p.y, brushSize * 2, 0, Math.PI * 2);
      ctx.fill();
      ctx.globalCompositeOperation = "source-over";
    } else {
      ctx.fillStyle = brushColor;
      ctx.arc(p.x, p.y, brushSize / 2, 0, Math.PI * 2);
      ctx.fill();
    }
  };

  const draw = (e) => {
    if (!isDrawing) return;
    e.preventDefault();
    const p = getPos(e);
    const ctx = canvasRef.current.getContext("2d");
    if (tool === "erase") {
      ctx.globalCompositeOperation = "destination-out";
      ctx.lineWidth = brushSize * 4;
      ctx.lineCap = "round";
      ctx.lineJoin = "round";
      ctx.beginPath();
      ctx.moveTo(lastPt.current.x, lastPt.current.y);
      ctx.lineTo(p.x, p.y);
      ctx.stroke();
      ctx.globalCompositeOperation = "source-over";
    } else {
      ctx.strokeStyle = brushColor;
      ctx.lineWidth = brushSize;
      ctx.lineCap = "round";
      ctx.lineJoin = "round";
      ctx.beginPath();
      ctx.moveTo(lastPt.current.x, lastPt.current.y);
      ctx.lineTo(p.x, p.y);
      ctx.stroke();
    }
    lastPt.current = p;
  };

  const endDraw = () => { setIsDrawing(false); lastPt.current = null; };

  const addToAquarium = () => {
    const c = canvasRef.current;
    const trimmed = trimCanvas(c);
    if (!trimmed) return;
    const src = trimmed.toDataURL("image/png");
    const maxDim = mode === "fish" ? 80 : 100;
    const scale = Math.min(maxDim / trimmed.width, maxDim / trimmed.height, 1.2);
    const id = idCounter.current++;

    if (mode === "fish") {
      setFishes((prev) => [
        ...prev,
        {
          id,
          src,
          width: trimmed.width,
          height: trimmed.height,
          scale,
          startX: 100 + Math.random() * (AQUARIUM_W - 200),
          startY: 60 + Math.random() * (AQUARIUM_H - 120),
        },
      ]);
    } else {
      setObjects((prev) => [
        ...prev,
        {
          id,
          src,
          width: trimmed.width,
          height: trimmed.height,
          scale,
          x: 40 + Math.random() * (AQUARIUM_W - 80),
          y: AQUARIUM_H - 40 - Math.random() * 60,
        },
      ]);
    }
    initCanvas();
  };

  const clearCanvas = () => initCanvas();
  const clearAquarium = () => { setFishes([]); setObjects([]); };

  return (
    <div style={{
      background: "linear-gradient(135deg, #0a1628 0%, #0d2137 40%, #0f2b46 100%)",
      fontFamily: "'Fredoka', 'Nunito', sans-serif",
      padding: "16px",
      boxSizing: "border-box",
      display: "flex",
      flexDirection: "column",
      alignItems: "center",
      gap: 16,
      borderRadius: 16,
    }}>
      <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;500;600;700&display=swap" rel="stylesheet" />
      <style>{`
        @keyframes bubbleUp {
          0% { transform: translateY(100%) scale(0.5); opacity: 0; }
          10% { opacity: 0.7; }
          90% { opacity: 0.3; }
          100% { transform: translateY(-120%) scale(1.2); opacity: 0; }
        }
        .bubble {
          position: absolute;
          bottom: 0;
          border-radius: 50%;
          background: radial-gradient(circle at 30% 30%, rgba(255,255,255,0.5), rgba(255,255,255,0.08));
          border: 1px solid rgba(255,255,255,0.15);
          animation: bubbleUp linear infinite;
          pointer-events: none;
        }
        @keyframes sway {
          0%, 100% { transform: rotate(-8deg); }
          50% { transform: rotate(8deg); }
        }
        @keyframes lightRay {
          0%, 100% { opacity: 0.04; }
          50% { opacity: 0.09; }
        }
      `}</style>

      <h1 style={{
        margin: 0,
        color: "#4dd9f7",
        fontSize: 28,
        fontWeight: 700,
        textShadow: "0 0 30px rgba(77,217,247,0.4)",
        letterSpacing: 1,
      }}>
        Aquarium Creator
      </h1>

      <div style={{ display: "flex", gap: 16, flexWrap: "wrap", justifyContent: "center", alignItems: "flex-start" }}>
        <div style={{
          background: "rgba(255,255,255,0.06)",
          borderRadius: 16,
          padding: 16,
          border: "1px solid rgba(255,255,255,0.1)",
          backdropFilter: "blur(10px)",
          display: "flex",
          flexDirection: "column",
          gap: 10,
          alignItems: "center",
          width: 300,
        }}>
          <div style={{ display: "flex", gap: 6, width: "100%" }}>
            {["fish", "object"].map((m) => (
              <button
                key={m}
                onClick={() => { setMode(m); initCanvas(); }}
                style={{
                  flex: 1,
                  padding: "10px 0",
                  borderRadius: 10,
                  border: mode === m ? "2px solid #4dd9f7" : "2px solid rgba(255,255,255,0.15)",
                  background: mode === m
                    ? "linear-gradient(135deg, rgba(77,217,247,0.25), rgba(77,217,247,0.08))"
                    : "rgba(255,255,255,0.04)",
                  color: mode === m ? "#4dd9f7" : "rgba(255,255,255,0.5)",
                  fontSize: 15,
                  fontWeight: 600,
                  cursor: "pointer",
                  fontFamily: "inherit",
                  transition: "all 0.2s",
                  textTransform: "capitalize",
                }}
              >
                {m === "fish" ? "🐟 Fish" : "🪨 Object"}
              </button>
            ))}
          </div>

          <div style={{
            fontSize: 12,
            color: "rgba(255,255,255,0.4)",
            textAlign: "center",
          }}>
            Draw a {mode} below
          </div>

          <div style={{
            position: "relative",
            borderRadius: 12,
            overflow: "hidden",
            border: "2px solid rgba(77,217,247,0.2)",
            background: "rgba(0,0,0,0.3)",
            touchAction: "none",
          }}>
            <canvas
              ref={canvasRef}
              width={CANVAS_SIZE}
              height={CANVAS_SIZE}
              style={{ width: 268, height: 268, display: "block", cursor: tool === "erase" ? "cell" : "crosshair" }}
              onMouseDown={startDraw}
              onMouseMove={draw}
              onMouseUp={endDraw}
              onMouseLeave={endDraw}
              onTouchStart={startDraw}
              onTouchMove={draw}
              onTouchEnd={endDraw}
            />
          </div>

          <div style={{ display: "flex", gap: 6, width: "100%" }}>
            <button
              onClick={() => setTool("draw")}
              style={{
                flex: 1, padding: "7px 0", borderRadius: 8, border: "none", fontSize: 13, fontWeight: 600,
                background: tool === "draw" ? "#4dd9f7" : "rgba(255,255,255,0.1)",
                color: tool === "draw" ? "#0a1628" : "rgba(255,255,255,0.5)",
                cursor: "pointer", fontFamily: "inherit",
              }}
            >Draw</button>
            <button
              onClick={() => setTool("erase")}
              style={{
                flex: 1, padding: "7px 0", borderRadius: 8, border: "none", fontSize: 13, fontWeight: 600,
                background: tool === "erase" ? "#EF476F" : "rgba(255,255,255,0.1)",
                color: tool === "erase" ? "#fff" : "rgba(255,255,255,0.5)",
                cursor: "pointer", fontFamily: "inherit",
              }}
            >Erase</button>
          </div>

          <div style={{ display: "flex", flexWrap: "wrap", gap: 4, justifyContent: "center" }}>
            {colors.map((c) => (
              <div
                key={c}
                onClick={() => { setBrushColor(c); setTool("draw"); }}
                style={{
                  width: 22, height: 22, borderRadius: 6,
                  background: c,
                  border: brushColor === c && tool === "draw" ? "2px solid #4dd9f7" : "2px solid rgba(255,255,255,0.15)",
                  cursor: "pointer",
                  boxShadow: brushColor === c && tool === "draw" ? "0 0 8px rgba(77,217,247,0.5)" : "none",
                  transition: "all 0.15s",
                }}
              />
            ))}
          </div>

          <div style={{ display: "flex", alignItems: "center", gap: 8, width: "100%" }}>
            <span style={{ color: "rgba(255,255,255,0.4)", fontSize: 11 }}>Size</span>
            <input
              type="range" min={1} max={16} value={brushSize}
              onChange={(e) => setBrushSize(Number(e.target.value))}
              style={{ flex: 1, accentColor: "#4dd9f7" }}
            />
            <div style={{
              width: Math.max(brushSize, 4) + 8,
              height: Math.max(brushSize, 4) + 8,
              borderRadius: "50%",
              background: tool === "erase" ? "#EF476F" : brushColor,
              border: "1px solid rgba(255,255,255,0.2)",
            }} />
          </div>

          <div style={{ display: "flex", gap: 6, width: "100%" }}>
            <button
              onClick={addToAquarium}
              style={{
                flex: 2, padding: "12px 0", borderRadius: 10, border: "none",
                background: "linear-gradient(135deg, #06D6A0, #118AB2)",
                color: "#fff", fontSize: 15, fontWeight: 700, cursor: "pointer",
                fontFamily: "inherit", boxShadow: "0 4px 15px rgba(6,214,160,0.3)",
                transition: "transform 0.15s",
              }}
              onMouseDown={(e) => e.currentTarget.style.transform = "scale(0.96)"}
              onMouseUp={(e) => e.currentTarget.style.transform = "scale(1)"}
            >
              Add to Aquarium
            </button>
            <button
              onClick={clearCanvas}
              style={{
                flex: 1, padding: "12px 0", borderRadius: 10, border: "1px solid rgba(255,255,255,0.15)",
                background: "rgba(255,255,255,0.05)", color: "rgba(255,255,255,0.5)",
                fontSize: 13, fontWeight: 600, cursor: "pointer", fontFamily: "inherit",
              }}
            >Clear</button>
          </div>
        </div>

        <div style={{
          position: "relative",
          width: AQUARIUM_W,
          maxWidth: "calc(100vw - 32px)",
          height: AQUARIUM_H,
          borderRadius: 20,
          overflow: "hidden",
          border: "3px solid rgba(77,217,247,0.2)",
          boxShadow: "0 0 60px rgba(77,217,247,0.1), inset 0 0 80px rgba(0,40,80,0.5)",
          background: "linear-gradient(180deg, #0a3d5c 0%, #0b4f6c 20%, #0e6377 50%, #0f5460 75%, #1a3a4a 100%)",
        }}>
          {[...Array(5)].map((_, i) => (
            <div key={i} style={{
              position: "absolute",
              top: -20,
              left: `${10 + i * 20}%`,
              width: 60 + i * 20,
              height: "110%",
              background: `linear-gradient(180deg, rgba(255,255,255,0.08) 0%, transparent 70%)`,
              transform: `rotate(${-8 + i * 4}deg)`,
              animation: `lightRay ${3 + i}s ease-in-out infinite`,
              animationDelay: `${i * 0.5}s`,
              pointerEvents: "none",
            }} />
          ))}

          <div style={{
            position: "absolute",
            bottom: 0,
            left: 0,
            right: 0,
            height: 50,
            background: "linear-gradient(180deg, #c4a265, #a8884e)",
            borderTop: "2px solid #d4b87a",
          }}>
            {[...Array(30)].map((_, i) => (
              <div key={i} style={{
                position: "absolute",
                width: 3 + Math.random() * 4,
                height: 2 + Math.random() * 3,
                borderRadius: "50%",
                background: `rgba(${180 + Math.random()*40},${140 + Math.random()*30},${70 + Math.random()*20},0.5)`,
                left: `${Math.random() * 100}%`,
                top: `${10 + Math.random() * 70}%`,
              }} />
            ))}
          </div>

          {[15, 35, 70, 85].map((x, i) => (
            <div key={i} style={{
              position: "absolute",
              bottom: 30,
              left: `${x}%`,
              width: 14 + i * 3,
              height: 50 + i * 20,
              background: `linear-gradient(0deg, #1a6b4a, #228b5a ${60}%, #2eaa6a)`,
              borderRadius: "40% 40% 0 0",
              transformOrigin: "bottom center",
              animation: `sway ${2.5 + i * 0.5}s ease-in-out infinite`,
              animationDelay: `${i * 0.3}s`,
              opacity: 0.7,
              pointerEvents: "none",
            }} />
          ))}

          {[...Array(12)].map((_, i) => <Bubble key={i} />)}

          {objects.map((obj) => (
            <img
              key={`obj-${obj.id}`}
              src={obj.src}
              alt="object"
              style={{
                position: "absolute",
                left: obj.x - (obj.width * obj.scale) / 2,
                top: obj.y - (obj.height * obj.scale) / 2,
                width: obj.width * obj.scale,
                height: obj.height * obj.scale,
                imageRendering: "pixelated",
                pointerEvents: "none",
                filter: "drop-shadow(0 2px 4px rgba(0,0,0,0.3))",
              }}
            />
          ))}

          {fishes.map((f) => (
            <Fish
              key={`fish-${f.id}`}
              data={f}
              allFish={fishes}
              aquariumW={AQUARIUM_W}
              aquariumH={AQUARIUM_H - 50}
              objects={objects}
            />
          ))}

          <div style={{
            position: "absolute",
            top: 10,
            right: 14,
            display: "flex",
            gap: 10,
            fontSize: 12,
            fontWeight: 600,
            color: "rgba(255,255,255,0.5)",
          }}>
            <span>🐟 {fishes.length}</span>
            <span>🪨 {objects.length}</span>
          </div>

          {fishes.length === 0 && objects.length === 0 && (
            <div style={{
              position: "absolute",
              inset: 0,
              display: "flex",
              alignItems: "center",
              justifyContent: "center",
              color: "rgba(255,255,255,0.2)",
              fontSize: 18,
              fontWeight: 500,
              fontFamily: "inherit",
              pointerEvents: "none",
            }}>
              Draw a fish and add it here!
            </div>
          )}
        </div>
      </div>

      {(fishes.length > 0 || objects.length > 0) && (
        <button
          onClick={clearAquarium}
          style={{
            padding: "8px 20px", borderRadius: 8,
            border: "1px solid rgba(239,71,111,0.3)",
            background: "rgba(239,71,111,0.1)",
            color: "#EF476F", fontSize: 13, fontWeight: 600,
            cursor: "pointer", fontFamily: "inherit",
          }}
        >
          Empty Aquarium
        </button>
      )}
    </div>
  );
}

ReactDOM.render(
  <Aquarium />,
  document.getElementById('aquarium-root')
);
// {% endraw %}
</script>
