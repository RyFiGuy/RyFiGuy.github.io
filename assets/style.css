let challenges = [];
let totalPoints = 0;

const els = {
  grid: document.getElementById('grid'),
  empty: document.getElementById('empty'),
  search: document.getElementById('search'),
  category: document.getElementById('category'),
  difficulty: document.getElementById('difficulty'),
  modal: document.getElementById('modal'),
  mTitle: document.getElementById('modalTitle'),
  mCat: document.getElementById('modalCat'),
  mDiff: document.getElementById('modalDiff'),
  mDate: document.getElementById('modalDate'),
  mBody: document.getElementById('modalBody'),
  points: document.getElementById('points')
};

async function loadChallenges() {
  const res = await fetch('data/challenges.json');
  challenges = await res.json();
  render();
  updatePointsDisplay();
}

function updatePointsDisplay() {
  totalPoints = JSON.parse(localStorage.getItem('lbctf_points') || '0');
  els.points.textContent = `Total Points: ${totalPoints}`;
}

function awardPoints(pts) {
  totalPoints += pts;
  localStorage.setItem('lbctf_points', JSON.stringify(totalPoints));
  updatePointsDisplay();
}

function difficultyBadgeClass(level) {
  if (level === 'easy') return 'badge diff-easy';
  if (level === 'medium') return 'badge diff-med';
  return 'badge diff-hard';
}

function render() {
  const q = els.search.value.trim().toLowerCase();
  const cat = (els.category.value || '').toLowerCase();
  const diff = (els.difficulty.value || '').toLowerCase();

  const list = challenges.filter(c => {
    const hay = (c.title + ' ' + c.body + ' ' + c.category + ' ' + c.difficulty).toLowerCase();
    const okQ = !q || hay.includes(q);
    const okC = !cat || c.category.toLowerCase() === cat;
    const okD = !diff || c.difficulty.toLowerCase() === diff;
    return okQ && okC && okD;
  });

  els.grid.innerHTML = '';
  if (list.length === 0) {
    els.empty.hidden = false;
    return;
  }
  els.empty.hidden = true;

  list.forEach(c => {
    const card = document.createElement('article');
    card.className = 'card';
    card.innerHTML = `
      <span class="date">${c.date ?? ''}</span>
      <h3>${c.title}</h3>
      <div class="meta">
        <span class="badge">${c.category}</span>
        <span class="${difficultyBadgeClass(c.difficulty)}">${c.difficulty}</span>
        <span class="badge">${c.points} pts</span>
      </div>
    `;
    card.onclick = () => openModal(c);
    els.grid.appendChild(card);
  });
}

function openModal(c) {
  els.mTitle.textContent = c.title;
  els.mCat.textContent = c.category;
  els.mCat.className = 'badge';
  els.mDiff.textContent = c.difficulty;
  els.mDiff.className = difficultyBadgeClass(c.difficulty);
  els.mDate.textContent = c.date || '';
  els.mBody.textContent = c.body || '';

  const verifyWrap = document.getElementById('verifyWrap');
  verifyWrap.style.display = c["flag-hash"] ? 'block' : 'none';
  if (c["flag-hash"]) {
    const input = document.getElementById('flagInput');
    const msg = document.getElementById('verifyMsg');
    const btn = document.getElementById('checkBtn');
    msg.textContent = '';
    input.value = '';
    btn.onclick = async () => {
      const ok = await checkFlag(input.value, c["flag-hash"]);
      msg.textContent = ok ? 'Correct!' : 'Incorrect';
      msg.style.color = ok ? 'var(--ok)' : 'var(--hard)';
      if (ok && !localStorage.getItem(`solved_${c.id}`)) {
        localStorage.setItem(`solved_${c.id}`, 'true');
        awardPoints(c.points);
      }
    };
  }
  els.modal.showModal();
}

async function checkFlag(flag, hashHex) {
  const enc = new TextEncoder();
  const data = enc.encode(flag.trim());
  const buf = await crypto.subtle.digest('SHA-256', data);
  const hex = Array.from(new Uint8Array(buf)).map(b => b.toString(16).padStart(2, '0')).join('');
  return hex === hashHex.toLowerCase();
}

// Wire filters
els.search.addEventListener('input', render);
els.category.addEventListener('change', render);
els.difficulty.addEventListener('change', render);

loadChallenges();
