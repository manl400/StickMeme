import React, { useEffect, useRef, useState } from "react";

// Explosive Stick Fight — Live P2P (no server)
// How it works:
// 1) Click "Host a Match" to generate an Offer (SDP). Copy it to your friend.
// 2) Your friend clicks "Join a Match", pastes the Offer, then copies back the Answer.
// 3) You paste their Answer. Boom — live peer‑to‑peer!
// Controls: P1 (host): A/D to move, W to jump, J punch, K kick, E explode (if you have a Meme Bomb)
//           P2 (guest): ←/→ move, ↑ jump, 1 punch, 2 kick, 0 explode (if you have a Meme Bomb)
// Tip: Grab 📦 Meme Crates for Meme Bombs. Make it go KABOOM 💥

// Tailwind is available.

// ---------- Helpers ----------
const clamp = (v, a, b) => Math.max(a, Math.min(b, v));
const rand = (a, b) => a + Math.random() * (b - a);
const lerp = (a, b, t) => a + (b - a) * t;

const MEMES = [
  "such wow",
  "deez nuts!",
  "what are those?!",
  "the dress: blue/black?",
  "left shark approves",
  "netflix and chill",
  "JOHN CENA 🎺",
  "i can has cheezburger",
  "wow. much skill. so fight.",
  "kermit sips tea",
  "pepe intensifies",
  "straight outta stamina",
  "it's over 9000!",
  "rekt",
  "no scope!!",
];

const WORLD = { W: 960, H: 540, GROUND: 460, GRAVITY: 1500 };

function useRaf(callback) {
  const ref = useRef(0);
  useEffect(() => {
    let last = performance.now();
    let id = 0;
    const tick = (t) => {
      const dt = (t - last) / 1000;
      last = t;
      callback(dt);
      id = requestAnimationFrame(tick);
    };
    id = requestAnimationFrame(tick);
    return () => cancelAnimationFrame(id);
  }, [callback]);
  return ref.current;
}

function makePlayer(name, color, x) {
  return {
    name,
    color,
    x,
    y: WORLD.GROUND,
    vx: 0,
    vy: 0,
    w: 18,
    h: 60,
    facing: 1,
    hp: 100,
    attackTimer: 0,
    attackType: null,
    attackCd: 0,
    onGround: true,
    hasBoom: 0,
    stun: 0,
    lastHitAt: 0,
  };
}

function drawStick(ctx, p) {
  // Body baseline
  const cx = p.x, cy = p.y;
  ctx.lineWidth = 4;
  ctx.strokeStyle = p.color;
  ctx.fillStyle = p.color;
  // Head
  ctx.beginPath();
  ctx.arc(cx, cy - p.h - 10, 10, 0, Math.PI * 2);
  ctx.fill();
  // Body
  ctx.beginPath();
  ctx.moveTo(cx, cy - p.h);
  ctx.lineTo(cx, cy - 20);
  ctx.stroke();
  // Arms
  const armDir = p.facing;
  ctx.beginPath();
  ctx.moveTo(cx, cy - p.h + 10);
  ctx.lineTo(cx + 18 * armDir, cy - p.h + 18);
  ctx.moveTo(cx, cy - p.h + 20);
  ctx.lineTo(cx - 18 * armDir, cy - p.h + 28);
  ctx.stroke();
  // Legs
  ctx.beginPath();
  ctx.moveTo(cx, cy - 20);
  ctx.lineTo(cx + 15, cy);
  ctx.moveTo(cx, cy - 20);
  ctx.lineTo(cx - 15, cy);
  ctx.stroke();

  // Attack flash
  if (p.attackTimer > 0) {
    ctx.globalAlpha = 0.25;
    ctx.fillStyle = "#fff";
    ctx.fillRect(cx - 25, cy - p.h - 30, 50, 70);
    ctx.globalAlpha = 1;
  }
}

function rectsOverlap(a, b) {
  return !(a.x + a.w < b.x || b.x + b.w < a.x || a.y + a.h < b.y || b.y + b.h < a.y);
}

export default function App() {
  const canvasRef = useRef(null);
  const [role, setRole] = useState(null); // 'host' | 'guest' | null
  const [status, setStatus] = useState("disconnected");
  const [offerText, setOfferText] = useState("");
  const [answerText, setAnswerText] = useState("");
  const pcRef = useRef(null);
  const dcRef = useRef(null);
  const [msg, setMsg] = useState("Welcome. Host or Join a match!");

  // Game state
  const localKeys = useRef({});
  const remoteKeys = useRef({});
  const hostAuthoritative = useRef(false);
  const [connectedPeer, setConnectedPeer] = useState(false);

  const stateRef = useRef({
    p1: makePlayer("P1", "#10b981", WORLD.W * 0.25),
    p2: makePlayer("P2", "#ef4444", WORLD.W * 0.75),
    particles: [],
    explosions: [],
    crates: [],
    memeTexts: [],
    shakeT: 0,
    winner: null,
    lastCrateSpawn: 0,
    time: 0,
  });

  // ---------- Networking (WebRTC DataChannel) ----------
  async function createPeer() {
    const pc = new RTCPeerConnection({
      iceServers: [{ urls: "stun:stun.l.google.com:19302" }],
    });
    pc.onicecandidate = () => {
      // Continuously update SDP with gathered ICE candidates
      if (pc.localDescription) {
        const s = JSON.stringify(pc.localDescription);
        if (role === "host") setOfferText(s);
        if (role === "guest") setAnswerText(s);
      }
    };
    pc.onconnectionstatechange = () => {
      setMsg(pc.connectionState);
      if (pc.connectionState === "connected") setConnectedPeer(true);
    };
    pcRef.current = pc;
    return pc;
  }

  async function startHost() {
    setRole("host");
    setStatus("connecting");
    hostAuthoritative.current = true;
    const pc = await createPeer();
    const dc = pc.createDataChannel("game", { ordered: true });
    wireChannel(dc);
    const offer = await pc.createOffer();
    await pc.setLocalDescription(offer);
    setOfferText(JSON.stringify(pc.localDescription));
    setMsg("Offer created. Share it with your friend.");
  }

  async function acceptAnswer() {
    try {
      const answer = JSON.parse(answerText);
      await pcRef.current.setRemoteDescription(answer);
      setStatus("connected");
      setMsg("Connected! Fight!");
    } catch (e) {
      setMsg("Invalid answer");
    }
  }

  async function startGuest() {
    setRole("guest");
    setStatus("connecting");
    hostAuthoritative.current = false;
    const pc = await createPeer();
    pc.ondatachannel = (ev) => {
      wireChannel(ev.channel);
    };
    try {
      const offer = JSON.parse(offerText);
      await pc.setRemoteDescription(offer);
      const answer = await pc.createAnswer();
      await pc.setLocalDescription(answer);
      setAnswerText(JSON.stringify(pc.localDescription));
      setMsg("Answer created. Send it back to the host.");
    } catch (e) {
      setMsg("Invalid offer");
    }
  }

  function wireChannel(dc) {
    dcRef.current = dc;
    dc.onopen = () => {
      setStatus("connected");
      setMsg("Connected! Ready to rumble.");
      // Start sending inputs periodically
      startInputLoop();
      if (role === "host") startStateLoop();
    };
    dc.onmessage = (ev) => {
      const data = JSON.parse(ev.data);
      if (data.type === "input") {
        remoteKeys.current = data.payload;
      } else if (data.type === "state" && role === "guest") {
        // Apply authoritative state from host
        const s = stateRef.current;
        Object.assign(s.p1, data.payload.p1);
        Object.assign(s.p2, data.payload.p2);
        s.explosions = data.payload.explosions || [];
        s.memeTexts = data.payload.memeTexts || [];
        s.particles = data.payload.particles || [];
        s.crates = data.payload.crates || [];
        s.winner = data.payload.winner || null;
        s.shakeT = data.payload.shakeT || 0;
      }
    };
  }

  function startInputLoop() {
    // Send inputs ~30fps
    const id = setInterval(() => {
      const dc = dcRef.current;
      if (dc && dc.readyState === "open") {
        try {
          dc.send(JSON.stringify({ type: "input", payload: localKeys.current }));
        } catch {}
      } else {
        clearInterval(id);
      }
    }, 33);
  }

  function startStateLoop() {
    // Host sends authoritative state ~10fps
    const id = setInterval(() => {
      const dc = dcRef.current;
      if (dc && dc.readyState === "open") {
        const s = stateRef.current;
        const payload = {
          p1: s.p1,
          p2: s.p2,
          explosions: s.explosions,
          memeTexts: s.memeTexts,
          particles: s.particles.slice(0, 60),
          crates: s.crates,
          shakeT: s.shakeT,
          winner: s.winner,
        };
        try {
          dc.send(JSON.stringify({ type: "state", payload }));
        } catch {}
      } else {
        clearInterval(id);
      }
    }, 100);
  }

  // ---------- Input Handling ----------
  useEffect(() => {
    const down = (e) => {
      if (e.repeat) return;
      const code = e.code;
      localKeys.current[code] = true;
    };
    const up = (e) => {
      const code = e.code;
      localKeys.current[code] = false;
    };
    window.addEventListener("keydown", down);
    window.addEventListener("keyup", up);
    return () => {
      window.removeEventListener("keydown", down);
      window.removeEventListener("keyup", up);
    };
  }, []);

  // ---------- Game Simulation ----------
  function spawnCrate(s) {
    s.crates.push({ x: rand(100, WORLD.W - 100), y: WORLD.GROUND - 20, w: 26, h: 20, t: 0 });
  }

  function spawnMemeText(s, text, x, y) {
    s.memeTexts.push({ text, x, y, t: 0, life: 1.6 });
  }

  function boom(s, x, y) {
    s.explosions.push({ x, y, r: 10, R: 140, t: 0, life: 0.35 });
    for (let i = 0; i < 80; i++) {
      s.particles.push({
        x,
        y,
        vx: rand(-300, 300),
        vy: rand(-500, -100),
        life: rand(0.4, 0.9),
        t: 0,
      });
    }
    s.shakeT = 0.5;
    spawnMemeText(s, MEMES[(Math.random() * MEMES.length) | 0], x, y - 40);
  }

  function tryHit(attacker, victim, kind, s) {
    const reach = kind === "punch" ? 26 : 36;
    const height = 24;
    const hx = attacker.x + attacker.facing * (attacker.w / 2 + reach);
    const hy = attacker.y - attacker.h + 28;
    const hb = { x: hx - 10, y: hy - height / 2, w: 20, h: height };
    const vb = { x: victim.x - 10, y: victim.y - victim.h, w: 20, h: victim.h };
    if (rectsOverlap(hb, vb)) {
      const dmg = kind === "punch" ? 6 : 10;
      victim.hp = clamp(victim.hp - dmg, 0, 100);
      const kb = kind === "punch" ? 220 : 350;
      victim.vx += attacker.facing * kb;
      victim.vy = -kb * 0.35;
      victim.stun = 0.18;
      s.shakeT = 0.1;
      spawnMemeText(s, ["rekt", "wow", "deez nuts!", "what are those?!"][Math.floor(Math.random()*4)], victim.x, victim.y - victim.h - 10);
      if (victim.hp <= 0 && !s.winner) {
        s.winner = attacker.name;
        spawnMemeText(s, "K.O.", WORLD.W / 2, WORLD.H / 2 - 40);
      }
    }
  }

  function stepHost(s, dt) {
    // Crates
    s.time += dt;
    if (s.time - s.lastCrateSpawn > 6) {
      s.lastCrateSpawn = s.time;
      if (Math.random() < 0.8) spawnCrate(s);
    }

    const p1 = s.p1, p2 = s.p2;
    const L = localKeys.current;
    const R = remoteKeys.current;

    // Map controls: host controls p1 (WASD/J/K/E), guest controls p2 (Arrows/1/2/0)
    const i1 = {
      left: !!L["KeyA"],
      right: !!L["KeyD"],
      up: !!L["KeyW"],
      punch: !!L["KeyJ"],
      kick: !!L["KeyK"],
      boom: !!L["KeyE"],
    };
    const i2 = {
      left: !!R["ArrowLeft"],
      right: !!R["ArrowRight"],
      up: !!R["ArrowUp"],
      punch: !!R["Digit1"],
      kick: !!R["Digit2"],
      boom: !!R["Digit0"],
    };

    physicsFor(p1, i1, dt, s);
    physicsFor(p2, i2, dt, s);

    // Resolve attacks
    if (p1.attackTimer > 0 && p1.attackType) tryHit(p1, p2, p1.attackType, s);
    if (p2.attackTimer > 0 && p2.attackType) tryHit(p2, p1, p2.attackType, s);

    // Explosions
    applyExplosions(s, p1, p2);

    // Crate pickups
    s.crates = s.crates.filter((c) => {
      const pb1 = { x: p1.x - 10, y: p1.y - p1.h, w: 20, h: p1.h };
      const pb2 = { x: p2.x - 10, y: p2.y - p2.h, w: 20, h: p2.h };
      const rb = { x: c.x - c.w / 2, y: c.y - c.h, w: c.w, h: c.h };
      if (rectsOverlap(pb1, rb)) {
        p1.hasBoom++;
        spawnMemeText(s, "+1 Meme Bomb", c.x, c.y - 18);
        return false;
      }
      if (rectsOverlap(pb2, rb)) {
        p2.hasBoom++;
        spawnMemeText(s, "+1 Meme Bomb", c.x, c.y - 18);
        return false;
      }
      c.t += dt;
      return true;
    });

    // Update particles & explosions & memes
    updateFx(s, dt);
  }

  function physicsFor(p, input, dt, s) {
    const ACC = 1200;
    const MAX = 320;
    const FRICTION = 800;

    if (p.stun > 0) p.stun -= dt;

    if (p.stun <= 0) {
      if (input.left) p.vx -= ACC * dt;
      if (input.right) p.vx += ACC * dt;
      if (input.up && p.onGround) {
        p.vy = -600;
        p.onGround = false;
      }
      if (input.punch && p.attackCd <= 0) {
        p.attackType = "punch";
        p.attackTimer = 0.12;
        p.attackCd = 0.35;
      }
      if (input.kick && p.attackCd <= 0) {
        p.attackType = "kick";
        p.attackTimer = 0.16;
        p.attackCd = 0.45;
      }
      if (input.boom && p.hasBoom > 0) {
        p.hasBoom--;
        boom(s, p.x + p.facing * 24, p.y - 30);
      }
    }

    p.facing = p.vx >= 0 ? 1 : -1;

    // Cooldowns
    if (p.attackTimer > 0) p.attackTimer -= dt; else p.attackType = null;
    if (p.attackCd > 0) p.attackCd -= dt;

    // Physics
    p.vx = clamp(p.vx, -MAX, MAX);
    if (!input.left && !input.right) {
      // friction
      if (p.vx > 0) p.vx = Math.max(0, p.vx - FRICTION * dt);
      else if (p.vx < 0) p.vx = Math.min(0, p.vx + FRICTION * dt);
    }
    p.vy += WORLD.GRAVITY * dt;
    p.x += p.vx * dt;
    p.y += p.vy * dt;

    // Ground & walls
    if (p.y >= WORLD.GROUND) {
      p.y = WORLD.GROUND;
      p.vy = 0;
      p.onGround = true;
    }
    p.x = clamp(p.x, 30, WORLD.W - 30);
  }

  function applyExplosions(s, p1, p2) {
    for (const ex of s.explosions) {
      const grow = ex.R * (ex.t / ex.life);
      const r = Math.min(ex.R, 10 + grow);
      ex.r = r;
      for (const p of [p1, p2]) {
        const dx = p.x - ex.x;
        const dy = (p.y - 30) - ex.y;
        const d = Math.hypot(dx, dy);
        if (d < r + 10) {
          const force = (1 - d / (r + 10)) * 900;
          const nx = dx / (d || 1);
          const ny = dy / (d || 1);
          p.vx += nx * force;
          p.vy += ny * force - 200;
          if (ex.t < 0.1) {
            p.hp = clamp(p.hp - 12, 0, 100);
            p.stun = 0.25;
          }
        }
      }
    }
  }

  function updateFx(s, dt) {
    // Explosions
    s.explosions = s.explosions.filter((ex) => {
      ex.t += dt;
      return ex.t < ex.life;
    });
    // Particles
    s.particles = s.particles.filter((p) => {
      p.t += dt;
      p.vy += WORLD.GRAVITY * dt * 0.5;
      p.x += p.vx * dt;
      p.y += p.vy * dt;
      return p.t < p.life && p.y < WORLD.GROUND + 5;
    });
    // Meme floating texts
    s.memeTexts = s.memeTexts.filter((m) => {
      m.t += dt;
      m.y -= 12 * dt;
      return m.t < m.life;
    });
    // Screen shake decay
    s.shakeT = Math.max(0, s.shakeT - dt);
  }

  // ---------- Render ----------
  useRaf((dt) => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext("2d");
    const s = stateRef.current;

    if (role === "host") {
      stepHost(s, dt);
    } else {
      // Guest: only update FX timers to keep visuals flowing
      updateFx(s, dt);
    }

    // Draw
    ctx.clearRect(0, 0, WORLD.W, WORLD.H);

    // Background gradient
    const g = ctx.createLinearGradient(0, 0, 0, WORLD.H);
    g.addColorStop(0, "#0ea5e9");
    g.addColorStop(1, "#111827");
    ctx.fillStyle = g;
    ctx.fillRect(0, 0, WORLD.W, WORLD.H);

    // Ground
    ctx.fillStyle = "#1f2937";
    ctx.fillRect(0, WORLD.GROUND, WORLD.W, WORLD.H - WORLD.GROUND);

    // Screen shake
    if (s.shakeT > 0) {
      const m = s.shakeT * 6;
      ctx.save();
      ctx.translate(rand(-m, m), rand(-m, m));
    }

    // Crates
    for (const c of s.crates) {
      const w = c.w, h = c.h;
      ctx.fillStyle = "#8b5cf6";
      ctx.fillRect(c.x - w/2, c.y - h, w, h);
      ctx.fillStyle = "#fff";
      ctx.font = "12px monospace";
      ctx.fillText("📦", c.x - 6, c.y - 4);
    }

    // Players
    drawStick(ctx, s.p1);
    drawStick(ctx, s.p2);

    // Attacks viz
    const showAttack = (p) => {
      if (p.attackTimer > 0 && p.attackType) {
        const reach = p.attackType === "punch" ? 26 : 36;
        const hx = p.x + p.facing * (p.w / 2 + reach);
        const hy = p.y - p.h + 28;
        ctx.strokeStyle = "#fde047";
        ctx.lineWidth = 2;
        ctx.strokeRect(hx - 10, hy - 12, 20, 24);
      }
    };
    showAttack(s.p1); showAttack(s.p2);

    // Explosions
    for (const ex of s.explosions) {
      const t = ex.t / ex.life;
      const r = lerp(10, ex.R, t);
      ctx.globalAlpha = 0.5 * (1 - t);
      const rad = ctx.createRadialGradient(ex.x, ex.y, 0, ex.x, ex.y, r);
      rad.addColorStop(0, "#fff");
      rad.addColorStop(0.4, "#f59e0b");
      rad.addColorStop(1, "rgba(239,68,68,0)");
      ctx.fillStyle = rad;
      ctx.beginPath();
      ctx.arc(ex.x, ex.y, r, 0, Math.PI * 2);
      ctx.fill();
      ctx.globalAlpha = 1;
    }

    // Particles
    ctx.fillStyle = "#f59e0b";
    for (const p of s.particles) {
      ctx.fillRect(p.x, p.y, 3, 3);
    }

    // Undo shake
    if (s.shakeT > 0) ctx.restore();

    // UI overlays: HP bars
    const drawBar = (x, y, w, h, v, color) => {
      ctx.fillStyle = "#111827";
      ctx.fillRect(x, y, w, h);
      ctx.fillStyle = color;
      ctx.fillRect(x + 2, y + 2, (w - 4) * (v / 100), h - 4);
    };
    drawBar(24, 20, 300, 16, s.p1.hp, "#10b981");
    drawBar(WORLD.W - 324, 20, 300, 16, s.p2.hp, "#ef4444");

    // Meme texts
    for (const m of s.memeTexts) {
      const a = 1 - m.t / m.life;
      ctx.globalAlpha = a;
      ctx.fillStyle = "#fff";
      ctx.font = "bold 20px Comic Sans MS, system-ui, sans-serif";
      ctx.textAlign = "center";
      ctx.fillText(m.text, m.x, m.y);
      ctx.globalAlpha = 1;
    }

    // Winner
    if (s.winner) {
      ctx.fillStyle = "#fff";
      ctx.font = "bold 48px Impact, system-ui, sans-serif";
      ctx.textAlign = "center";
      ctx.fillText(`${s.winner} wins — REKT!`, WORLD.W / 2, WORLD.H / 2);
      ctx.font = "20px monospace";
      ctx.fillText("Press R to reset", WORLD.W / 2, WORLD.H / 2 + 36);
    }
  });

  // Reset handler
  useEffect(() => {
    const onR = (e) => {
      if ((e.code === "KeyR" || e.code === "KeyEnter") && stateRef.current.winner) {
        const s = stateRef.current;
        s.p1 = makePlayer("P1", "#10b981", WORLD.W * 0.25);
        s.p2 = makePlayer("P2", "#ef4444", WORLD.W * 0.75);
        s.particles = [];
        s.explosions = [];
        s.crates = [];
        s.memeTexts = [];
        s.winner = null;
      }
    };
    window.addEventListener("keydown", onR);
    return () => window.removeEventListener("keydown", onR);
  }, []);

  // ---------- UI ----------
  return (
    <div className="min-h-screen bg-slate-900 text-slate-100 flex flex-col items-center gap-4 p-4">
      <h1 className="text-3xl font-black tracking-tight flex items-center gap-3 mt-2">
        💥 Explosive Stick Fight <span className="text-sm font-semibold px-2 py-1 rounded-xl bg-fuchsia-600/30">2015 Meme Edition</span>
      </h1>

      <div className="grid grid-cols-1 lg:grid-cols-12 gap-4 w-full max-w-6xl">
        <div className="lg:col-span-8 flex flex-col items-center gap-2">
          <canvas
            ref={canvasRef}
            width={WORLD.W}
            height={WORLD.H}
            className="rounded-2xl shadow-2xl ring-1 ring-white/10 bg-black"
          />
          <div className="text-xs text-slate-300/90 mt-1 text-center">
            Host: WASD + J/K to attack, E to explode. Guest: Arrows + 1/2 attack, 0 explode. Grab 📦 for Meme Bombs.
          </div>
        </div>

        <div className="lg:col-span-4 space-y-3">
          <div className="p-3 rounded-2xl bg-slate-800/70 border border-white/10">
            <div className="text-sm mb-2 opacity-90">Connection</div>
            <div className="flex gap-2">
              <button onClick={startHost} className="px-3 py-2 rounded-xl bg-emerald-600 hover:bg-emerald-500 disabled:opacity-40" disabled={status!=="disconnected" && role!=="host"}>Host a Match</button>
              <button onClick={startGuest} className="px-3 py-2 rounded-xl bg-indigo-600 hover:bg-indigo-500 disabled:opacity-40" disabled={status!=="disconnected" && role!=="guest"}>Join a Match</button>
            </div>
            <div className="text-xs mt-2 opacity-90">Status: <span className="font-semibold">{status}</span> — {msg}</div>
          </div>

          <div className="p-3 rounded-2xl bg-slate-800/70 border border-white/10 space-y-2">
            <div className="text-sm opacity-90 font-semibold">Step 1: Share Offer (Host → Guest)</div>
            <textarea value={offerText} onChange={(e)=>setOfferText(e.target.value)} rows={6} className="w-full p-2 rounded-xl bg-slate-900/70 border border-white/10 text-xs font-mono" placeholder="(Host) After clicking Host a Match, copy this Offer and send to your friend.\n(Guest) Paste their Offer here before you click Join a Match."/>
            {role==="host" && <button className="px-2 py-1 rounded-lg bg-slate-700 hover:bg-slate-600 text-xs" onClick={()=>navigator.clipboard?.writeText(offerText)}>Copy Offer</button>}
          </div>

          <div className="p-3 rounded-2xl bg-slate-800/70 border border-white/10 space-y-2">
            <div className="text-sm opacity-90 font-semibold">Step 2: Return Answer (Guest → Host)</div>
            <textarea value={answerText} onChange={(e)=>setAnswerText(e.target.value)} rows={6} className="w-full p-2 rounded-xl bg-slate-900/70 border border-white/10 text-xs font-mono" placeholder="(Guest) After clicking Join, an Answer appears here — copy it back to host.\n(Host) Paste the Answer here then click 'Accept Answer'."/>
            {role==="guest" && <button className="px-2 py-1 rounded-lg bg-slate-700 hover:bg-slate-600 text-xs" onClick={()=>navigator.clipboard?.writeText(answerText)}>Copy Answer</button>}
            {role==="host" && <button className="px-2 py-1 rounded-lg bg-pink-600 hover:bg-pink-500 text-xs" onClick={acceptAnswer}>Accept Answer</button>}
          </div>

          <div className="p-3 rounded-2xl bg-slate-800/70 border border-white/10 text-xs leading-relaxed">
            <div className="font-semibold mb-1">How to play online without a server</div>
            <ol className="list-decimal list-inside space-y-1 opacity-90">
              <li>One of you clicks <span className="font-semibold">Host a Match</span> and sends the Offer text to the other.</li>
              <li>The other clicks <span className="font-semibold">Join a Match</span>, pastes the Offer, then copies back the Answer.</li>
              <li>The host pastes the Answer and clicks <span className="font-semibold">Accept Answer</span>. GG!</li>
            </ol>
            <div className="mt-2">If the connection struggles, try again — peer-to-peer needs both browsers to allow WebRTC.</div>
          </div>
        </div>
      </div>

      <footer className="text-xs opacity-70 text-center max-w-3xl mt-1">
        2015 throwbacks will randomly appear on hits & booms: "what are those?!", "deez nuts", "left shark", "JOHN CENA", and more. Please meme responsibly.
      </footer>
    </div>
  );
}
