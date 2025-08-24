<!DOCTYPE html>
<html lang="sv">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Demo Casinospel – Spela gratis</title>
  <meta name="description" content="Spela demo casinospel gratis. Testa slots i free play-läge och hitta licensierade casinon.">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <style>
    body { font-family: system-ui, -apple-system, Segoe UI, Roboto, sans-serif; margin:0; }
    header { position:sticky; top:0; background:#fff; border-bottom:1px solid #eee; padding:12px 16px; display:flex; gap:12px; align-items:center; z-index:10;}
    .brand { font-weight:700; }
    #search { flex:1; padding:10px 12px; border:1px solid #ddd; border-radius:8px; }
    .grid { display:grid; grid-template-columns: repeat(auto-fill, minmax(220px,1fr)); gap:16px; padding:16px; }
    .card { border:1px solid #eee; border-radius:12px; overflow:hidden; background:#fff; display:flex; flex-direction:column; }
    .thumb { aspect-ratio:16/9; background:#f4f4f4; display:flex; align-items:center; justify-content:center; font-size:14px; color:#777; }
    .card-body { padding:12px; display:flex; flex-direction:column; gap:6px; }
    .meta { font-size:12px; color:#666; display:flex; gap:8px; flex-wrap:wrap; }
    .tags { display:flex; gap:6px; flex-wrap:wrap; }
    .tag { font-size:11px; background:#f2f2f2; padding:4px 8px; border-radius:999px; }
    .row { display:flex; gap:8px; }
    .btn { flex:1; padding:10px 12px; border-radius:8px; border:1px solid #ddd; background:#fafafa; cursor:pointer; text-align:center; text-decoration:none; }
    .btn.primary { background:#0e7afe; color:white; border-color:#0e7afe; }
    /* Modal */
    dialog { width:min(1100px, 96vw); border:none; border-radius:12px; padding:0; }
    .modal-header { display:flex; justify-content:space-between; align-items:center; padding:10px 12px; border-bottom:1px solid #eee; }
    .modal-body { height:min(70vh, 80dvh); }
    iframe { width:100%; height:100%; border:0; }
    .notice { padding:8px 12px; font-size:12px; color:#555; background:#fffbe6; border-top:1px solid #f2e7a8; }
    footer { padding:24px 16px; font-size:12px; color:#666; border-top:1px solid #eee; }
    .age-gate { position:fixed; inset:0; background:rgba(255,255,255,0.96); display:none; align-items:center; justify-content:center; }
    .age-box { background:#fff; border:1px solid #eee; border-radius:12px; padding:20px; width:min(420px,90vw); text-align:center; }
  </style>
</head>
<body>
  <div class="age-gate" id="ageGate">
    <div class="age-box">
      <h2>18+</h2>
      <p>Den här sidan visar demo av casinospel. Bekräfta att du är 18 år eller äldre.</p>
      <div class="row" style="margin-top:12px;">
        <button class="btn" id="ageNo">Nej</button>
        <button class="btn primary" id="ageYes">Ja, jag är 18+</button>
      </div>
      <p style="font-size:12px;margin-top:8px;">Spela ansvarsfullt. I Sverige: besök Stödlinjen vid behov.</p>
    </div>
  </div>

  <header>
    <div class="brand">Demo Casinospel</div>
    <input id="search" type="search" placeholder="Sök spel, leverantör, tagg…" />
  </header>

  <main class="grid" id="grid" aria-live="polite"></main>

  <dialog id="player">
    <div class="modal-header">
      <div id="modalTitle">Speltitel</div>
      <button class="btn" id="closeBtn">Stäng</button>
    </div>
    <div class="modal-body">
      <iframe id="iframe" title="Speldemo" loading="lazy" referrerpolicy="no-referrer-when-downgrade" allow="autoplay; fullscreen"></iframe>
    </div>
    <div class="notice">
      Demo-läge. 18+. Spela ansvarsfullt. <a id="affLink" class="btn primary" style="margin-left:8px;">Spela på riktigt (annons)</a>
    </div>
  </dialog>

  <footer>
    <strong>Ansvarsfriskrivning:</strong> Vi visar enbart demos. Länkar till partners kan vara annons-/affiliate-länkar och vi kan få ersättning. Spela endast hos licensierade operatörer. 18+. 
  </footer>

  <script>
    const grid = document.getElementById('grid');
    const search = document.getElementById('search');
    const dlg = document.getElementById('player');
    const iframe = document.getElementById('iframe');
    const modalTitle = document.getElementById('modalTitle');
    const affLink = document.getElementById('affLink');

    fetch('games.json').then(r => r.json()).then(games => {
      const state = { games, filtered: games };

      const render = () => {
        grid.innerHTML = '';
        state.filtered.forEach(g => {
          const el = document.createElement('article');
          el.className = 'card';
          el.innerHTML = `
            <div class="thumb" aria-hidden="true">${g.provider}</div>
            <div class="card-body">
              <div><strong>${g.title}</strong></div>
              <div class="meta">
                <span>${g.category}</span>
                <span>RTP: ${g.rtp ? g.rtp + '%' : '—'}</span>
                <span>${g.provider}</span>
              </div>
              <div class="tags">${(g.tags||[]).map(t=>`<span class="tag">${t}</span>`).join('')}</div>
              <div class="row">
                <button class="btn" data-demo="${g.demo_url}" data-title="${g.title}" data-aff="${g.affiliate_url}">Spela demo</button>
                <a class="btn primary" href="${g.affiliate_url}" rel="nofollow sponsored noopener" target="_blank" aria-label="Spela på riktigt hos licensierad operatör">Spela på riktigt</a>
              </div>
            </div>`;
          el.querySelector('button').addEventListener('click', (e) => {
            const btn = e.currentTarget;
            iframe.src = btn.dataset.demo;
            modalTitle.textContent = btn.dataset.title;
            affLink.href = btn.dataset.aff;
            affLink.setAttribute('rel','nofollow sponsored noopener');
            affLink.setAttribute('target','_blank');
            if (!dlg.open) dlg.showModal();
          });
          grid.appendChild(el);
        });
      };

      search.addEventListener('input', () => {
        const q = search.value.toLowerCase().trim();
        state.filtered = state.games.filter(g => (
          g.title.toLowerCase().includes(q) ||
          (g.provider||'').toLowerCase().includes(q) ||
          (g.category||'').toLowerCase().includes(q) ||
          (g.tags||[]).some(t => t.toLowerCase().includes(q))
        ));
        render();
      });

      render();
    });

    document.getElementById('closeBtn').addEventListener('click', () => {
      iframe.src = 'about:blank';
      dlg.close();
    });

    // Enkel 18+ åldersgrind
    const gate = document.getElementById('ageGate');
    const ok = localStorage.getItem('age_ok');
    if (!ok) gate.style.display = 'flex';
    document.getElementById('ageYes').onclick = () => { localStorage.setItem('age_ok','1'); gate.style.display = 'none'; };
    document.getElementById('ageNo').onclick = () => { window.location.href = 'https://www.google.com'; };
  </script>
</body>
</html>


[
  {
    "id": "sweet-bonanza",
    "title": "Sweet Bonanza",
    "provider": "Pragmatic Play",
    "category": "Slot",
    "rtp": 96.5,
    "tags": ["hög volatilitet", "multiplikator"],
    "demo_url": "https://demo.example-aggregator.com/pragmatic/sweet-bonanza/index.html",
    "affiliate_url": "https://ditt-partnerprogram.com/land?cid=123&utm_source=site&utm_medium=button&utm_campaign=sweet-bonanza"
  },
  {
    "id": "book-of-dead",
    "title": "Book of Dead",
    "provider": "Play'n GO",
    "category": "Slot",
    "rtp": 96.2,
    "tags": ["klassiker"],
    "demo_url": "https://demo.example-aggregator.com/playngo/book-of-dead/index.html",
    "affiliate_url": "https://ditt-partnerprogram.com/land?cid=123&utm_source=site&utm_medium=button&utm_campaign=book-of-dead"
  }
]
