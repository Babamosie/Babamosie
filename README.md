import { useState, useEffect, useRef } from "react";

const THEMES = {
  "midnight": {
    bg: "#0d1117", border: "#30363d", title: "#58a6ff",
    text: "#c9d1d9", icon: "#58a6ff", bar: "#1f6feb", barBg: "#21262d",
    ring: "#58a6ff", label: "#8b949e"
  },
  "radical": {
    bg: "#141321", border: "#fe428e", title: "#fe428e",
    text: "#a9fef7", icon: "#f8d847", bar: "#fe428e", barBg: "#2d2a3e",
    ring: "#fe428e", label: "#a9fef7"
  },
  "tokyonight": {
    bg: "#1a1b27", border: "#414868", title: "#70a5fd",
    text: "#cdd6f4", icon: "#bf91f3", bar: "#70a5fd", barBg: "#252537",
    ring: "#bf91f3", label: "#787c99"
  },
  "synthwave": {
    bg: "#2b213a", border: "#e9609e", title: "#e9609e",
    text: "#f1daff", icon: "#f1daff", bar: "#e9609e", barBg: "#3d2c52",
    ring: "#f1daff", label: "#b79fcb"
  },
  "catppuccin": {
    bg: "#1e1e2e", border: "#cba6f7", title: "#cba6f7",
    text: "#cdd6f4", icon: "#89b4fa", bar: "#cba6f7", barBg: "#313244",
    ring: "#a6e3a1", label: "#a6adc8"
  },
  "nord": {
    bg: "#2e3440", border: "#81a1c1", title: "#88c0d0",
    text: "#d8dee9", icon: "#81a1c1", bar: "#5e81ac", barBg: "#3b4252",
    ring: "#a3be8c", label: "#8fbcbb"
  },
};

const ICONS = {
  star: `<path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>`,
  fork: `<path d="M7 3a2 2 0 1 0 0 4 2 2 0 0 0 0-4zM5 5a2 2 0 1 1 4 0 2 2 0 0 1-4 0zm7 14a2 2 0 1 0 0 4 2 2 0 0 0 0-4zm-2 2a2 2 0 1 1 4 0 2 2 0 0 1-4 0zm7-14a2 2 0 1 0 0 4 2 2 0 0 0 0-4zm-2 2a2 2 0 1 1 4 0 2 2 0 0 1-4 0zM7 7v2c0 1.1.9 2 2 2h6a2 2 0 0 0 2-2v-.5M12 11v8"/>`,
  commit: `<circle cx="12" cy="12" r="3"/><line x1="12" y1="3" x2="12" y2="9"/><line x1="12" y1="15" x2="12" y2="21"/><line x1="3" y1="12" x2="9" y2="12"/><line x1="15" y1="12" x2="21" y2="12"/>`,
  pr: `<circle cx="18" cy="18" r="3"/><circle cx="6" cy="6" r="3"/><path d="M13 6h3a2 2 0 0 1 2 2v7"/><line x1="6" y1="9" x2="6" y2="21"/>`,
  issue: `<circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/>`,
  review: `<path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/>`,
  followers: `<path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/>`,
};

function StatCard({ data, theme, cardType, username }) {
  const t = THEMES[theme];
  const svgRef = useRef();

  const fmt = (n) => n >= 1000 ? (n / 1000).toFixed(1) + "k" : String(n ?? 0);

  const statsCard = () => {
    const stats = [
      { label: "Total Stars", value: fmt(data.stars), icon: "star" },
      { label: "Total Forks", value: fmt(data.forks), icon: "fork" },
      { label: "Commits", value: fmt(data.commits), icon: "commit" },
      { label: "Pull Requests", value: fmt(data.prs), icon: "pr" },
      { label: "Issues", value: fmt(data.issues), icon: "issue" },
      { label: "Followers", value: fmt(data.followers), icon: "followers" },
    ];
    const maxVal = Math.max(data.stars, data.forks, data.commits, data.prs, data.issues, data.followers, 1);
    return `<svg xmlns="http://www.w3.org/2000/svg" width="495" height="195" viewBox="0 0 495 195">
  <style>
    .title { font: 600 18px 'Segoe UI', sans-serif; fill: ${t.title}; }
    .stat-label { font: 400 12px 'Segoe UI', sans-serif; fill: ${t.label}; }
    .stat-value { font: 700 14px 'Segoe UI', sans-serif; fill: ${t.text}; }
    .sub { font: 400 12px 'Segoe UI', sans-serif; fill: ${t.label}; }
  </style>
  <rect width="495" height="195" rx="10" fill="${t.bg}" stroke="${t.border}" stroke-width="1"/>
  <text x="25" y="35" class="title">${data.name || username}'s GitHub Stats</text>
  ${stats.map((s, i) => {
    const row = Math.floor(i / 3);
    const col = i % 3;
    const x = 25 + col * 155;
    const y = 65 + row * 60;
    const pct = Math.max(((data[Object.keys({stars:1,forks:1,commits:1,prs:1,issues:1,followers:1})[i]] || 0) / maxVal) * 80, 4);
    return `<g>
      <svg x="${x}" y="${y}" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="${t.icon}" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">${ICONS[s.icon]}</svg>
      <text x="${x+22}" y="${y+12}" class="stat-value">${s.value}</text>
      <text x="${x}" y="${y+28}" class="stat-label">${s.label}</text>
      <rect x="${x}" y="${y+34}" width="120" height="5" rx="2" fill="${t.barBg}"/>
      <rect x="${x}" y="${y+34}" width="${pct}" height="5" rx="2" fill="${t.bar}"/>
    </g>`;
  }).join("")}
</svg>`;
  };

  const topLangsCard = () => {
    const langs = data.languages || [];
    const total = langs.reduce((a, b) => a + b.count, 0) || 1;
    const colors = ["#58a6ff","#fe428e","#f8d847","#bf91f3","#a9fef7","#89b4fa"];
    let barX = 25;
    const barWidth = 445;
    return `<svg xmlns="http://www.w3.org/2000/svg" width="495" height="${60 + Math.ceil(langs.length / 2) * 42 + 40}" viewBox="0 0 495 ${60 + Math.ceil(langs.length / 2) * 42 + 40}">
  <style>
    .title { font: 600 18px 'Segoe UI', sans-serif; fill: ${t.title}; }
    .lang { font: 400 13px 'Segoe UI', sans-serif; fill: ${t.text}; }
    .pct { font: 700 13px 'Segoe UI', sans-serif; fill: ${t.label}; }
  </style>
  <rect width="495" height="${60 + Math.ceil(langs.length / 2) * 42 + 40}" rx="10" fill="${t.bg}" stroke="${t.border}" stroke-width="1"/>
  <text x="25" y="35" class="title">Top Languages</text>
  <rect x="25" y="55" width="${barWidth}" height="10" rx="5" fill="${t.barBg}"/>
  ${langs.map((l, i) => {
    const pct = (l.count / total);
    const w = Math.max(pct * barWidth, 2);
    const seg = `<rect x="${barX}" y="55" width="${w}" height="10" rx="${i===0?5:0}" fill="${colors[i % colors.length]}"/>`;
    barX += w;
    return seg;
  }).join("")}
  ${langs.map((l, i) => {
    const col = i % 2;
    const row = Math.floor(i / 2);
    const x = 25 + col * 240;
    const y = 90 + row * 42;
    const pct = ((l.count / total) * 100).toFixed(1);
    return `<circle cx="${x+8}" cy="${y+6}" r="7" fill="${colors[i % colors.length]}"/>
    <text x="${x+22}" y="${y+11}" class="lang">${l.name}</text>
    <text x="${x+22}" y="${y+27}" class="pct">${pct}%</text>`;
  }).join("")}
</svg>`;
  };

  const svg = cardType === "stats" ? statsCard() : topLangsCard();

  return (
    <div style={{display:"flex", flexDirection:"column", gap:"12px"}}>
      <div
        style={{borderRadius:"var(--border-radius-lg)", overflow:"hidden", background:t.bg, border:`1px solid ${t.border}`, display:"inline-block"}}
        dangerouslySetInnerHTML={{__html: svg}}
      />
      <div style={{display:"flex", gap:"8px"}}>
        <button
          onClick={() => {
            const blob = new Blob([svg], {type:"image/svg+xml"});
            const url = URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.href = url; a.download = `github-stats-${username}.svg`; a.click();
          }}
          style={{fontSize:"13px", padding:"6px 14px"}}
        >Download SVG</button>
        <button
          onClick={() => {
            const md = cardType === "stats"
              ? `![${username}'s GitHub Stats](https://github-readme-stats.vercel.app/api?username=${username}&theme=${theme}&show_icons=true&count_private=true)`
              : `![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=${username}&layout=compact&theme=${theme})`;
            navigator.clipboard.writeText(md);
          }}
          style={{fontSize:"13px", padding:"6px 14px"}}
        >Copy Markdown ↗</button>
      </div>
    </div>
  );
}

export default function App() {
  const [username, setUsername] = useState("");
  const [input, setInput] = useState("");
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");
  const [theme, setTheme] = useState("midnight");
  const [cardType, setCardType] = useState("stats");

  const fetchStats = async (u) => {
    setLoading(true); setError(""); setData(null);
    try {
      const [userRes, reposRes] = await Promise.all([
        fetch(`https://api.github.com/users/${u}`),
        fetch(`https://api.github.com/users/${u}/repos?per_page=100&sort=pushed`)
      ]);
      if (!userRes.ok) throw new Error(userRes.status === 404 ? "User not found" : "GitHub API error");
      const user = await userRes.json();
      const repos = await reposRes.json();
      const stars = repos.reduce((a, r) => a + r.stargazers_count, 0);
      const forks = repos.reduce((a, r) => a + r.forks_count, 0);
      const langMap = {};
      repos.forEach(r => { if (r.language) langMap[r.language] = (langMap[r.language] || 0) + 1; });
      const languages = Object.entries(langMap).sort((a,b)=>b[1]-a[1]).slice(0,8).map(([name,count])=>({name,count}));
      let commits = 0, prs = 0, issues = 0;
      try {
        const [cRes, pRes, iRes] = await Promise.all([
          fetch(`https://api.github.com/search/commits?q=author:${u}&per_page=1`, {headers:{"Accept":"application/vnd.github.cloak-preview"}}),
          fetch(`https://api.github.com/search/issues?q=author:${u}+type:pr&per_page=1`),
          fetch(`https://api.github.com/search/issues?q=author:${u}+type:issue&per_page=1`),
        ]);
        if (cRes.ok) commits = (await cRes.json()).total_count || 0;
        if (pRes.ok) prs = (await pRes.json()).total_count || 0;
        if (iRes.ok) issues = (await iRes.json()).total_count || 0;
      } catch {}
      setData({ name: user.name, stars, forks, commits, prs, issues, followers: user.followers, languages });
      setUsername(u);
    } catch(e) { setError(e.message); }
    setLoading(false);
  };

  return (
    <div style={{padding:"1.5rem 0", fontFamily:"var(--font-sans)", maxWidth:"640px"}}>
      <h2 className="sr-only">GitHub README Stats Generator</h2>

      <div style={{display:"flex", gap:"8px", marginBottom:"1.5rem"}}>
        <input
          value={input}
          onChange={e => setInput(e.target.value)}
          onKeyDown={e => e.key==="Enter" && input.trim() && fetchStats(input.trim())}
          placeholder="GitHub username…"
          style={{flex:1, fontFamily:"var(--font-mono)", fontSize:"14px"}}
        />
        <button
          onClick={() => input.trim() && fetchStats(input.trim())}
          disabled={loading || !input.trim()}
          style={{padding:"0 20px", fontSize:"14px"}}
        >{loading ? "Loading…" : "Generate"}</button>
      </div>

      {error && <p style={{color:"var(--color-text-danger)", fontSize:"14px", margin:"0 0 1rem"}}>{error}</p>}

      {data && (
        <div style={{display:"flex", flexDirection:"column", gap:"1.5rem"}}>
          <div style={{display:"flex", gap:"1rem", flexWrap:"wrap"}}>
            <div>
              <p style={{fontSize:"12px", color:"var(--color-text-secondary)", margin:"0 0 6px"}}>Card type</p>
              <div style={{display:"flex", gap:"6px"}}>
                {["stats","languages"].map(t => (
                  <button key={t} onClick={() => setCardType(t==="stats"?"stats":"langs")}
                    style={{fontSize:"12px", padding:"4px 12px", background: cardType===(t==="stats"?"stats":"langs") ? "var(--color-background-info)" : "transparent", color: cardType===(t==="stats"?"stats":"langs") ? "var(--color-text-info)" : "var(--color-text-secondary)"}}>
                    {t}
                  </button>
                ))}
              </div>
            </div>
            <div>
              <p style={{fontSize:"12px", color:"var(--color-text-secondary)", margin:"0 0 6px"}}>Theme</p>
              <div style={{display:"flex", gap:"6px", flexWrap:"wrap"}}>
                {Object.keys(THEMES).map(th => (
                  <button key={th} onClick={() => setTheme(th)}
                    style={{fontSize:"12px", padding:"4px 10px", background: th===theme ? THEMES[th].bg : "transparent", color: th===theme ? THEMES[th].title : "var(--color-text-secondary)", border: `1px solid ${th===theme ? THEMES[th].border : "var(--color-border-tertiary)"}`, borderRadius:"var(--border-radius-md)"}}>
                    {th}
                  </button>
                ))}
              </div>
            </div>
          </div>

          <StatCard data={data} theme={theme} cardType={cardType} username={username} />

          <div style={{background:"var(--color-background-secondary)", borderRadius:"var(--border-radius-md)", padding:"12px 16px", fontSize:"13px", fontFamily:"var(--font-mono)", color:"var(--color-text-secondary)", wordBreak:"break-all"}}>
            {cardType === "stats"
              ? `![${username}'s GitHub Stats](https://github-readme-stats.vercel.app/api?username=${username}&theme=${theme}&show_icons=true)`
              : `![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=${username}&layout=compact&theme=${theme})`}
          </div>
        </div>
      )}

      {!data && !loading && (
        <div style={{textAlign:"center", color:"var(--color-text-secondary)", fontSize:"14px", paddingTop:"2rem"}}>
          Babamosie
        </div>
      )}
    </div>
  );
}
