# El-Paso
import { useState } from “react”;

const WBC_TEAMS = [
“🇺🇸 USA”, “🇯🇵 Japan”, “🇩🇴 Dominican Republic”, “🇲🇽 Mexico”, “🇵🇷 Puerto Rico”,
“🇻🇪 Venezuela”, “🇰🇷 South Korea”, “🇨🇺 Cuba”, “🇨🇦 Canada”, “🇮🇹 Italy”,
“🇳🇱 Netherlands”, “🇦🇺 Australia”, “🇹🇼 Chinese Taipei”, “🇬🇧 Great Britain”,
“🇵🇦 Panama”, “🇮🇱 Israel”, “🇨🇴 Colombia”, “🇳🇮 Nicaragua”, “🇧🇷 Brazil”, “🇨🇿 Czechia”
];

const BATTING_CATS = [“R”, “HR”, “RBI”, “SB”, “AVG”, “OBP”, “SLG”];
const PITCHING_CATS = [“K”, “QS”, “W”, “BAA”, “ERA”, “WHIP”];

const questions = [
{
id: “buyin”,
type: “multiple_choice”,
question: “💰 What should the buy-in be this year?”,
options: [”$50 (same as last year)”, “$75”, “$100”],
},
{
id: “update_cats”,
type: “yes_no_text”,
question: “📋 Should we update any scoring categories?”,
subLabel: “Which categories would you change or add?”,
placeholder: “e.g. Add K/9, remove QS, swap OBP for OPS…”,
},
{
id: “num_cats”,
type: “multiple_choice”,
question: `📊 Should we change the number of categories?\nBatting: ${BATTING_CATS.join(", ")}\nPitching: ${PITCHING_CATS.join(", ")}`,
options: [
“Keep it the same (7 batting, 6 pitching)”,
“Add more categories”,
“Reduce categories”,
“Total overhaul 🔥”,
],
},
{
id: “mvp”,
type: “short_text”,
question: “🏆 Who wins the 2026 MLB MVP?”,
placeholder: “e.g. Aaron Judge, Shohei Ohtani…”,
},
{
id: “wbc”,
type: “multiple_choice”,
question: “⚾ Who wins the 2026 World Baseball Classic?”,
options: WBC_TEAMS,
scrollable: true,
},
{
id: “jake_james”,
type: “multiple_choice”,
question: “🤔 What happens first: Jake gets admitted to the bar OR James Wood wins an MVP?”,
options: [
“Jake passes the bar 📚”,
“James Wood wins MVP 🏆”,
“Neither happens 😬”,
“Both happen same year 🤯”,
],
},
{
id: “suggestions”,
type: “short_text”,
question: “💬 Suggestion box — anything else for the commissioner?”,
placeholder: “Rule changes, punishment ideas, trash talk…”,
optional: true,
},
];

export default function FantasyBaseballSurvey() {
const [current, setCurrent] = useState(0);
const [answers, setAnswers] = useState({});
const [name, setName] = useState(””);
const [submitted, setSubmitted] = useState(false);
const [started, setStarted] = useState(false);

const q = questions[current];
const isLast = current === questions.length - 1;
const progress = (current / questions.length) * 100;

const handleChoice = (val) => setAnswers((p) => ({ …p, [q.id]: val }));

const canProceed = () => {
if (!q) return false;
if (q.optional) return true;
if (q.type === “multiple_choice”) return !!answers[q.id];
if (q.type === “yes_no_text”) {
if (!answers[q.id + “_yn”]) return false;
if (answers[q.id + “_yn”] === “yes” && !answers[q.id + “_text”]?.trim()) return false;
return true;
}
if (q.type === “short_text”) return !!answers[q.id]?.trim();
return true;
};

const handleNext = () => { if (isLast) setSubmitted(true); else setCurrent((c) => c + 1); };
const handleBack = () => { if (current > 0) setCurrent((c) => c - 1); };
const reset = () => { setSubmitted(false); setCurrent(0); setAnswers({}); setName(””); setStarted(false); };

if (submitted) {
return (
<div style={s.page}>
<div style={s.card}>
<div style={{ fontSize: 56, marginBottom: 12 }}>🏆</div>
<h2 style={s.successTitle}>Locked in, {name || “Commish”}!</h2>
<p style={{ color: “#aac”, marginBottom: 24, lineHeight: 1.6 }}>
Your picks are saved. Now go tell your league mates to fill this out.
</p>
<div style={s.summaryBox}>
{questions.map((q) => {
let answer = “”;
if (q.type === “yes_no_text”) {
const yn = answers[q.id + “_yn”];
const txt = answers[q.id + “_text”];
answer = yn === “yes” ? `Yes — ${txt}` : yn === “no” ? “No changes” : “”;
} else {
answer = answers[q.id];
}
if (!answer) return null;
return (
<div key={q.id} style={s.summaryItem}>
<span style={s.summaryQ}>{q.question.split(”\n”)[0].replace(/^[^\s]+\s/, “”)}</span>
<span style={s.summaryA}>{answer}</span>
</div>
);
})}
</div>
<button style={s.restartBtn} onClick={reset}>Submit Another Response</button>
</div>
</div>
);
}

if (!started) {
return (
<div style={s.page}>
<div style={s.card}>
<div style={s.badge}>2026 SEASON</div>
<div style={{ fontSize: 64, margin: “12px 0” }}>⚾</div>
<h1 style={s.heroTitle}>Fantasy Baseball<br />League Survey</h1>
<p style={{ color: “#aac”, fontSize: 16, margin: “0 0 28px”, lineHeight: 1.6 }}>
Help shape the 2026 league. Takes about 2 minutes.
</p>
<input
style={s.nameInput}
placeholder=“Your name (optional)”
value={name}
onChange={(e) => setName(e.target.value)}
/>
<button style={s.startBtn} onClick={() => setStarted(true)}>
Let’s Play Ball →
</button>
<p style={{ color: “#445”, textAlign: “center”, marginTop: 14, fontSize: 14 }}>
{questions.length} questions
</p>
</div>
</div>
);
}

return (
<div style={s.page}>
<div style={s.card}>
<div style={s.progressTrack}>
<div style={{ …s.progressFill, width: `${progress}%` }} />
</div>
<div style={{ color: “#778”, fontSize: 13, marginBottom: 26, fontFamily: “Arial, sans-serif” }}>
{current + 1} of {questions.length}
{q.optional && <span style={{ marginLeft: 8, color: “#445”, fontStyle: “italic” }}>optional</span>}
</div>

```
    <div style={{ marginBottom: 28 }}>
      <p style={s.questionText}>
        {q.question.split("\n").map((line, i) => (
          <span key={i}>
            {i === 0 ? line : <span style={{ fontSize: 14, color: "#889", fontStyle: "normal", display: "block", marginTop: 6 }}>{line}</span>}
          </span>
        ))}
      </p>

      {q.type === "multiple_choice" && !q.scrollable && (
        <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
          {q.options.map((opt) => (
            <button
              key={opt}
              style={{ ...s.optBtn, ...(answers[q.id] === opt ? s.optSelected : {}) }}
              onClick={() => handleChoice(opt)}
            >
              {answers[q.id] === opt && <span style={{ fontWeight: 700 }}>✓ </span>}{opt}
            </button>
          ))}
        </div>
      )}

      {q.type === "multiple_choice" && q.scrollable && (
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8, maxHeight: 300, overflowY: "auto", paddingRight: 2 }}>
          {q.options.map((opt) => (
            <button
              key={opt}
              style={{ ...s.optBtn, ...(answers[q.id] === opt ? s.optSelected : {}), fontSize: 14, padding: "10px 12px" }}
              onClick={() => handleChoice(opt)}
            >
              {answers[q.id] === opt && "✓ "}{opt}
            </button>
          ))}
        </div>
      )}

      {q.type === "yes_no_text" && (
        <div>
          <div style={{ display: "flex", gap: 10, marginBottom: 14 }}>
            {["yes", "no"].map((v) => (
              <button
                key={v}
                style={{ ...s.optBtn, flex: 1, ...(answers[q.id + "_yn"] === v ? s.optSelected : {}) }}
                onClick={() => setAnswers((p) => ({ ...p, [q.id + "_yn"]: v }))}
              >
                {answers[q.id + "_yn"] === v && "✓ "}{v === "yes" ? "Yes" : "No"}
              </button>
            ))}
          </div>
          {answers[q.id + "_yn"] === "yes" && (
            <div>
              <p style={{ color: "#889", fontSize: 14, marginBottom: 8 }}>{q.subLabel}</p>
              <textarea
                style={s.textarea}
                placeholder={q.placeholder}
                value={answers[q.id + "_text"] || ""}
                onChange={(e) => setAnswers((p) => ({ ...p, [q.id + "_text"]: e.target.value }))}
                rows={3}
              />
            </div>
          )}
        </div>
      )}

      {q.type === "short_text" && (
        <textarea
          style={s.textarea}
          placeholder={q.placeholder}
          value={answers[q.id] || ""}
          onChange={(e) => handleChoice(e.target.value)}
          rows={3}
        />
      )}
    </div>

    <div style={{ display: "flex", gap: 12 }}>
      <button style={s.backBtn} onClick={handleBack} disabled={current === 0}>← Back</button>
      <button
        style={{ ...s.nextBtn, opacity: canProceed() ? 1 : 0.35, cursor: canProceed() ? "pointer" : "not-allowed" }}
        onClick={handleNext}
        disabled={!canProceed()}
      >
        {isLast ? "Submit ⚾" : "Next →"}
      </button>
    </div>
  </div>
</div>
```

);
}

const s = {
page: {
minHeight: “100vh”,
background: “linear-gradient(135deg, #080f0a 0%, #0c1c18 50%, #080e14 100%)”,
display: “flex”, alignItems: “center”, justifyContent: “center”,
fontFamily: “Georgia, serif”, padding: 20,
},
card: {
background: “rgba(255,255,255,0.04)”,
border: “1px solid rgba(255,255,255,0.08)”,
borderRadius: 20, padding: “40px 36px”, maxWidth: 540, width: “100%”,
backdropFilter: “blur(14px)”, boxShadow: “0 28px 70px rgba(0,0,0,0.6)”,
},
badge: {
display: “inline-block”, background: “#e8c84a”, color: “#1a1a1a”,
fontSize: 11, fontWeight: 700, letterSpacing: 3, padding: “4px 12px”,
borderRadius: 20, marginBottom: 20, fontFamily: “Arial, sans-serif”,
},
heroTitle: { fontSize: 34, color: “#fff”, margin: “0 0 10px”, lineHeight: 1.2, fontStyle: “italic” },
nameInput: {
width: “100%”, padding: “13px 16px”, borderRadius: 12,
border: “1px solid rgba(255,255,255,0.13)”, background: “rgba(255,255,255,0.07)”,
color: “#fff”, fontSize: 16, outline: “none”, marginBottom: 14,
boxSizing: “border-box”, fontFamily: “inherit”,
},
startBtn: {
width: “100%”, padding: 16, background: “#e8c84a”, color: “#1a1a1a”,
border: “none”, borderRadius: 12, fontSize: 17, fontWeight: 700,
cursor: “pointer”, fontFamily: “inherit”,
},
progressTrack: { height: 4, background: “rgba(255,255,255,0.08)”, borderRadius: 4, marginBottom: 8, overflow: “hidden” },
progressFill: { height: “100%”, background: “#e8c84a”, borderRadius: 4, transition: “width 0.4s ease” },
questionText: { color: “#fff”, fontSize: 19, lineHeight: 1.5, marginBottom: 20, fontStyle: “italic” },
optBtn: {
padding: “13px 16px”, background: “rgba(255,255,255,0.05)”,
border: “1px solid rgba(255,255,255,0.1)”, borderRadius: 12, color: “#ccc”,
fontSize: 15, cursor: “pointer”, textAlign: “left”, fontFamily: “inherit”,
transition: “all 0.15s”,
},
optSelected: { background: “rgba(232,200,74,0.14)”, border: “1px solid #e8c84a”, color: “#e8c84a” },
textarea: {
width: “100%”, padding: “13px 16px”, borderRadius: 12,
border: “1px solid rgba(255,255,255,0.13)”, background: “rgba(255,255,255,0.06)”,
color: “#fff”, fontSize: 15, outline: “none”, resize: “vertical”,
fontFamily: “inherit”, boxSizing: “border-box”, lineHeight: 1.5,
},
backBtn: {
padding: “13px 18px”, background: “transparent”,
border: “1px solid rgba(255,255,255,0.13)”, borderRadius: 12,
color: “#999”, fontSize: 15, cursor: “pointer”, fontFamily: “inherit”,
},
nextBtn: {
flex: 1, padding: “13px 18px”, background: “#e8c84a”, border: “none”,
borderRadius: 12, color: “#1a1a1a”, fontSize: 15, fontWeight: 700,
fontFamily: “inherit”, transition: “opacity 0.2s”,
},
successTitle: { color: “#e8c84a”, fontSize: 26, margin: “0 0 10px”, fontStyle: “italic” },
summaryBox: {
background: “rgba(255,255,255,0.03)”, border: “1px solid rgba(255,255,255,0.07)”,
borderRadius: 12, padding: 16, marginBottom: 24, display: “flex”,
flexDirection: “column”, gap: 12,
},
summaryItem: { display: “flex”, flexDirection: “column”, gap: 3 },
summaryQ: { color: “#667”, fontSize: 12, fontFamily: “Arial, sans-serif” },
summaryA: { color: “#e8c84a”, fontSize: 14, fontStyle: “italic” },
restartBtn: {
width: “100%”, padding: 14, background: “transparent”,
border: “1px solid rgba(255,255,255,0.15)”, borderRadius: 12,
color: “#888”, fontSize: 15, cursor: “pointer”, fontFamily: “inherit”,
},
};