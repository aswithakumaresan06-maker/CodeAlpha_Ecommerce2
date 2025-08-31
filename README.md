# CodeAlpha_Ecommerce2
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>FaceLook — Single‑File Social App (Local Only)</title>
  <meta name="description" content="A single-file Facebook-style clone using only HTML/CSS/JS with localStorage. No backend required." />
  <style>
    :root{
      --bg: #0b0f14;
      --panel: #11161d;
      --muted: #5f6b7a;
      --text: #e6edf3;
      --text-dim: #b9c4d0;
      --brand: #0ea5e9;
      --brand-2: #22c55e;
      --warn: #f59e0b;
      --danger: #ef4444;
      --ring: rgba(14,165,233,.45);
      --card: #0f141b;
      --input: #0c1218;
      --border: #1f2937;
      --shadow: 0 10px 30px rgba(0,0,0,.35), 0 2px 8px rgba(0,0,0,.25);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font: 15px/1.35 system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, "Helvetica Neue", Arial;
      background: linear-gradient(180deg, #0b0f14, #091018 40%, #0a111a);
      color:var(--text);
    }
    a{color:inherit; text-decoration:none}
    button{cursor:pointer}
    .hidden{display:none !important}
    .app{
      display:grid; grid-template-rows: 56px 1fr; height:100%;
    }
    /* Topbar */
    .topbar{
      position:sticky; top:0; z-index:10; height:56px; backdrop-filter: blur(10px);
      display:flex; gap:12px; align-items:center; padding:10px 16px; border-bottom:1px solid var(--border);
      background: linear-gradient(180deg, rgba(11,15,20,.8), rgba(11,15,20,.55));
    }
    .brand{
      display:flex; align-items:center; gap:10px; font-weight:800; letter-spacing:.2px;
    }
    .brand .logo{width:32px; height:32px; display:grid; place-items:center; border-radius:10px; background:radial-gradient(circle at 30% 30%, #38bdf8, #2563eb 70%); box-shadow:inset 0 0 30px rgba(255,255,255,.2), 0 6px 20px rgba(37,99,235,.35)}
    .brand .logo span{font-weight:900}
    .search{
      flex:1; max-width:680px; display:flex; align-items:center; background:var(--input); border:1px solid var(--border); border-radius:14px; padding:6px 10px; gap:8px;
    }
    .search input{flex:1; background:transparent; border:0; outline:none; color:var(--text); font-size:14px}
    .top-actions{margin-left:auto; display:flex; gap:10px}
    .btn{border:1px solid var(--border); background:linear-gradient(180deg, #0d131a, #0b1118); color:var(--text); border-radius:12px; padding:8px 12px; display:inline-flex; align-items:center; gap:8px; box-shadow:var(--shadow)}
    .btn:focus{outline:2px solid var(--ring)}
    .btn.primary{background:linear-gradient(180deg, #0ea5e9, #0284c7); border-color:#0284c7}
    .btn.warn{background:linear-gradient(180deg, #f59e0b, #d97706); border-color:#b45309}
    .btn.ghost{background:transparent; box-shadow:none}

    /* Layout */
    .layout{display:grid; grid-template-columns: 280px minmax(280px, 800px) 320px; gap:16px; padding:16px;}
    @media (max-width: 1200px){ .layout{grid-template-columns: 240px 1fr 280px} }
    @media (max-width: 980px){ .layout{grid-template-columns: 1fr} .sidebar, .contacts{display:none} }

    .panel{background:linear-gradient(180deg, var(--panel), #0d131a); border:1px solid var(--border); border-radius:18px; box-shadow:var(--shadow)}
    .sidebar{padding:14px}
    .contacts{padding:14px}

    .nav h4{margin:8px 0 10px; color:var(--text-dim); font-weight:700; text-transform:uppercase; letter-spacing:.08em; font-size:11px}
    .nav a{display:flex; gap:10px; padding:10px; border-radius:12px; color:var(--text-dim)}
    .nav a.active, .nav a:hover{background:#0f1722; color:var(--text)}

    /* Feed */
    .composer, .post{background:linear-gradient(180deg, var(--card), #0b1219); border:1px solid var(--border); border-radius:18px; padding:12px; margin-bottom:12px}
    .composer textarea{width:100%; background:transparent; border:0; outline:none; color:var(--text); min-height:64px; resize:vertical}
    .row{display:flex; align-items:center; gap:10px}
    .avatar{width:38px; height:38px; border-radius:12px; background:#1f2937; object-fit:cover}
    .muted{color:var(--muted)}
    .stack{display:flex; flex-direction:column; gap:6px}
    .grid{display:grid; gap:12px}
    .post .media{border-radius:14px; overflow:hidden; border:1px solid var(--border); max-height:520px}
    .post .media img{display:block; width:100%; height:auto}
    .post .actions{display:flex; gap:10px}
    .chip{font-size:11px; padding:4px 8px; border-radius:999px; background:#0e1622; border:1px solid var(--border); color:var(--text-dim)}

    .comment-box{display:flex; gap:8px; margin-top:8px}
    .comment-box input{flex:1; background:var(--input); border:1px solid var(--border); color:var(--text); padding:8px 10px; border-radius:10px}

    /* Contacts */
    .list{display:flex; flex-direction:column; gap:8px}
    .contact{display:flex; gap:10px; align-items:center; padding:8px; border-radius:12px}
    .contact:hover{background:#0f1722}
    .status{width:8px; height:8px; border-radius:999px; background:#16a34a}

    /* Modals */
    dialog{border:1px solid var(--border); background:linear-gradient(180deg, var(--panel), #0d151d); color:var(--text); border-radius:16px; padding:0; width:min(720px, 92vw)}
    dialog::backdrop{background:rgba(1,5,12,.6); backdrop-filter: blur(6px)}
    .modal-header{padding:12px 16px; border-bottom:1px solid var(--border); display:flex; align-items:center; justify-content:space-between}
    .modal-body{padding:14px}

    /* Auth */
    .auth{display:grid; place-items:center; height:100vh; padding:16px}
    .card{max-width:840px; width:100%; padding:18px; border-radius:18px; border:1px solid var(--border); background:linear-gradient(180deg, var(--panel), #0d131a); box-shadow: var(--shadow)}
    .two-col{display:grid; grid-template-columns: 1.2fr 1fr; gap:18px}
    @media (max-width:900px){ .two-col{grid-template-columns:1fr} }
    .field{display:flex; flex-direction:column; gap:6px}
    .field input, .field select, .field textarea{
      padding:10px 12px; border-radius:12px; border:1px solid var(--border); background:var(--input); color:var(--text)
    }
    .hint{font-size:12px; color:var(--text-dim)}

    /* Toast */
    .toast{position:fixed; bottom:16px; left:50%; transform: translateX(-50%); background:linear-gradient(180deg, #111827, #0b1220); padding:10px 14px; border:1px solid var(--border); border-radius:12px; box-shadow:var(--shadow); z-index:50}

    /* Utilities */
    .center{display:grid; place-items:center}
    .right{margin-left:auto}
    .space{flex:1}
    .small{font-size:12px}
    .xs{font-size:11px}
    .sep{height:1px; background:var(--border); margin:8px 0}

  </style>
</head>
<body>
<div id="root"></div>

<template id="tpl-sidebar">
  <div class="panel sidebar">
    <div class="row" style="margin-bottom:12px">
      <img class="avatar" id="sb-avatar"/>
      <div class="stack" style="min-width:0">
        <div id="sb-name" style="font-weight:700"></div>
        <div class="muted small" id="sb-username"></div>
      </div>
      <div class="space"></div>
      <button class="btn ghost small" id="sb-edit">Edit</button>
    </div>
    <nav class="nav">
      <h4>Explore</h4>
      <a href="#feed" data-route="#feed" class="active">🏠 Feed</a>
      <a href="#profile" data-route="#profile">👤 Profile</a>
      <a href="#friends" data-route="#friends">👥 Friends</a>
      <a href="#messages" data-route="#messages">💬 Messages</a>
      <a href="#notifications" data-route="#notifications">🔔 Notifications <span id="nav-notif" class="chip">0</span></a>
      <a href="#settings" data-route="#settings">⚙️ Settings</a>
    </nav>
  </div>
</template>

<template id="tpl-contacts">
  <div class="panel contacts">
    <div class="row" style="justify-content:space-between; margin-bottom:8px">
      <div style="font-weight:700">Contacts</div>
      <button class="btn ghost small" id="new-chat">New</button>
    </div>
    <div class="list" id="contacts-list"></div>
  </div>
</template>

<dialog id="modal-post">
  <div class="modal-header"><div style="font-weight:700">Create post</div><button class="btn ghost" onclick="closeModal('modal-post')">✖</button></div>
  <div class="modal-body">
    <div class="row" style="gap:12px">
      <img class="avatar" id="composer-avatar"/>
      <div class="stack" style="flex:1">
        <textarea id="composer-text" placeholder="What's on your mind?"></textarea>
        <div class="row" style="justify-content:space-between; flex-wrap:wrap; gap:8px">
          <div class="row" style="gap:8px">
            <label class="btn small">📷 Image<input type="file" id="composer-image" accept="image/*" hidden></label>
            <input class="chip" id="composer-image-url" placeholder="or paste image URL"/>
            <select class="chip" id="composer-privacy">
              <option value="public">🌍 Public</option>
              <option value="friends">👥 Friends</option>
              <option value="private">🔒 Only me</option>
            </select>
          </div>
          <button class="btn primary" id="composer-publish">Post</button>
        </div>
      </div>
    </div>
  </div>
</dialog>

<dialog id="modal-profile">
  <div class="modal-header"><div style="font-weight:700">Edit profile</div><button class="btn ghost" onclick="closeModal('modal-profile')">✖</button></div>
  <div class="modal-body grid">
    <div class="field">
      <label>Display name</label>
      <input id="pf-name"/>
    </div>
    <div class="field">
      <label>Bio</label>
      <textarea id="pf-bio" rows="3"></textarea>
    </div>
    <div class="row" style="gap:12px; align-items:end">
      <div class="field" style="flex:1">
        <label>Avatar URL</label>
        <input id="pf-avatar-url"/>
      </div>
      <label class="btn small">Upload<input type="file" id="pf-avatar-file" accept="image/*" hidden></label>
    </div>
    <div class="row" style="justify-content:end"><button class="btn primary" id="pf-save">Save</button></div>
  </div>
</dialog>

<div class="toast hidden" id="toast"></div>

<script>
/********************
 * Local-Only Social App
 * - No backend; uses localStorage
 * - Minimal, single-file 'Facebook-style' clone
 ********************/

const $ = (sel, root=document) => root.querySelector(sel);
const $$ = (sel, root=document) => Array.from(root.querySelectorAll(sel));
const uid = () => Math.random().toString(36).slice(2) + Date.now().toString(36);
const now = () => new Date().toISOString();

const db = {
  get(key, fallback){
    try{ return JSON.parse(localStorage.getItem(key)) ?? fallback }catch{ return fallback }
  },
  set(key, val){ localStorage.setItem(key, JSON.stringify(val)); }
};

const seed = () => {
  if (db.get('seeded')) return;
  const u1 = { id: uid(), username:'demo', name:'Demo User', bio:'Trying out FaceLook ✨', avatar:'https://picsum.photos/seed/demo/200', friends:[], createdAt:now()};
  const u2 = { id: uid(), username:'alex', name:'Alex Chen', bio:'Coffee, code, climbing.', avatar:'https://picsum.photos/seed/alex/200', friends:[], createdAt:now()};
  const users = [u1,u2];
  db.set('users', users);
  const posts = [
    { id:uid(), userId:u2.id, text:'Hello world from a single-file app! 🚀', image:'https://picsum.photos/seed/hello/900/500', privacy:'public', likes:[], comments:[], createdAt: now() },
    { id:uid(), userId:u1.id, text:'It\'s a good day to ship.', image:null, privacy:'public', likes:[], comments:[], createdAt: now() },
  ];
  db.set('posts', posts);
  db.set('messages', []);
  db.set('friendRequests', []);
  db.set('notifications', []);
  db.set('sessions', { currentUserId: u1.id });
  db.set('seeded', true);
};

// Auth & Session
const session = {
  get current(){ const s = db.get('sessions', {}); return getUserById(s.currentUserId) },
  set currentUserId(id){ const s = db.get('sessions', {}); s.currentUserId = id; db.set('sessions', s) },
}

function getUsers(){ return db.get('users', []) }
function setUsers(list){ db.set('users', list) }
function getUserById(id){ return getUsers().find(u=>u.id===id) }
function getUserByUsername(name){ return getUsers().find(u=>u.username===name) }

function ensureLoggedIn(){ if(!session.current){ renderAuth(); throw new Error('Not logged in') } }

// Posts
function getPosts(){ return db.get('posts', []) }
function setPosts(list){ db.set('posts', list) }

function createPost({text, image, privacy}){
  const user = session.current; if(!user) return;
  const post = { id:uid(), userId:user.id, text, image:image||null, privacy, likes:[], comments:[], createdAt: now() };
  const posts = [post, ...getPosts()];
  setPosts(posts);
  notifyFriends(user.id, 'posted', post.id);
  toast('Posted!');
  return post;
}

function toggleLike(postId){
  const user = session.current; if(!user) return;
  const posts = getPosts();
  const p = posts.find(p=>p.id===postId);
  if(!p) return;
  const i = p.likes.indexOf(user.id);
  if(i>=0) p.likes.splice(i,1); else p.likes.push(user.id);
  setPosts(posts);
  if(i<0) notify(p.userId, `${user.name} liked your post`, {postId});
}

function addComment(postId, text){
  const user = session.current; if(!user || !text.trim()) return;
  const posts = getPosts();
  const p = posts.find(p=>p.id===postId);
  if(!p) return;
  p.comments.push({ id:uid(), userId:user.id, text, createdAt:now()});
  setPosts(posts);
  if(user.id!==p.userId) notify(p.userId, `${user.name} commented on your post`, {postId});
}

// Friends
function getFriendRequests(){ return db.get('friendRequests', []) }
function setFriendRequests(list){ db.set('friendRequests', list) }

function sendFriendRequest(toUserId){
  const from = session.current; if(!from) return;
  if(from.id===toUserId) return toast('That\'s you!');
  const existing = getFriendRequests().find(fr=>fr.to===toUserId && fr.from===from.id && fr.status==='pending');
  if(existing) return toast('Request already sent.');
  const fr = { id:uid(), from:from.id, to:toUserId, status:'pending', createdAt:now() };
  setFriendRequests([fr, ...getFriendRequests()]);
  notify(toUserId, `${from.name} sent you a friend request`, {frId: fr.id});
}

function respondFriendRequest(frId, accept){
  const list = getFriendRequests();
  const fr = list.find(x=>x.id===frId); if(!fr) return;
  fr.status = accept? 'accepted':'declined';
  setFriendRequests(list);
  if(accept){
    const users = getUsers();
    const a = users.find(u=>u.id===fr.from); const b = users.find(u=>u.id===fr.to);
    if(a && b){ if(!a.friends.includes(b.id)) a.friends.push(b.id); if(!b.friends.includes(a.id)) b.friends.push(a.id); setUsers(users) }
  }
}

// Messages
function getMessages(){ return db.get('messages', []) }
function setMessages(list){ db.set('messages', list) }
function sendMessage(toUserId, text){
  const from = session.current; if(!from || !text.trim()) return;
  const msg = { id:uid(), from:from.id, to:toUserId, text, createdAt:now() };
  setMessages([ ...getMessages(), msg ]);
  notify(toUserId, `${from.name} sent you a message`, {userId: from.id});
}

// Notifications
function getNotifications(){ return db.get('notifications', []) }
function setNotifications(list){ db.set('notifications', list) }
function notify(userId, text, data={}){
  const n = { id:uid(), userId, text, data, read:false, createdAt:now() };
  setNotifications([n, ...getNotifications()]);
}
function notifyFriends(userId, text, refId){
  const me = getUserById(userId); if(!me) return;
  me.friends.forEach(fid => notify(fid, `${me.name} ${text}`, {postId: refId}));
}

// Storage helpers
async function readFileAsDataURL(file){
  return await new Promise((res, rej)=>{ const r=new FileReader(); r.onload=()=>res(r.result); r.onerror=rej; r.readAsDataURL(file) })
}

// ---------- UI ----------
function renderApp(){
  if(!session.current) return renderAuth();
  const root = document.getElementById('root');
  root.innerHTML = `
    <div class="app">
      <header class="topbar">
        <div class="brand"><div class="logo"><span>f</span></div> FaceLook</div>
        <div class="search"><span>🔎</span><input id="top-search" placeholder="Search posts, people"/></div>
        <div class="top-actions">
          <button class="btn" id="btn-new-post">➕ Create</button>
          <button class="btn ghost" id="btn-logout">Log out</button>
        </div>
      </header>
      <main class="layout">
        <aside id="col-left"></aside>
        <section id="col-main"></section>
        <aside id="col-right"></aside>
      </main>
    </div>`;

  // Sidebar & contacts
  $('#col-left').appendChild(document.importNode($('#tpl-sidebar').content, true));
  $('#col-right').appendChild(document.importNode($('#tpl-contacts').content, true));

  bindGlobal();
  updateSidebar();
  renderRoute(location.hash || '#feed');
}

function bindGlobal(){
  $('#btn-logout').onclick = ()=>{ db.set('sessions', {}); renderAuth() };
  $('#btn-new-post').onclick = ()=> openModal('modal-post');
  $('#composer-publish').onclick = async ()=>{
    const text = $('#composer-text').value.trim();
    if(!text && !$('#composer-image').files[0] && !$('#composer-image-url').value.trim()) return toast('Write something or add an image.');
    let img = $('#composer-image-url').value.trim() || null;
    if(!img && $('#composer-image').files[0]) img = await readFileAsDataURL($('#composer-image').files[0]);
    createPost({text, image: img, privacy: $('#composer-privacy').value});
    closeModal('modal-post'); $('#composer-text').value=''; $('#composer-image').value=''; $('#composer-image-url').value='';
    renderRoute('#feed');
  };
  $('#sb-edit').onclick = ()=> openModal('modal-profile');
  $('#pf-save').onclick = async ()=>{
    const u = session.current; const users = getUsers(); const me = users.find(x=>x.id===u.id);
    me.name = $('#pf-name').value.trim()||me.name; me.bio = $('#pf-bio').value; const url = $('#pf-avatar-url').value.trim();
    if($('#pf-avatar-file').files[0]) me.avatar = await readFileAsDataURL($('#pf-avatar-file').files[0]);
    else if(url) me.avatar = url;
    setUsers(users); closeModal('modal-profile'); updateSidebar(); renderRoute('#profile');
  };
  $('#top-search').oninput = (e)=> renderFeed(e.target.value.trim().toLowerCase());
}

function updateSidebar(){
  const u = session.current; if(!u) return;
  $('#sb-avatar').src = u.avatar; $('#composer-avatar').src = u.avatar; $('#sb-name').textContent = u.name; $('#sb-username').textContent = '@'+u.username;
  $('#pf-name').value = u.name; $('#pf-bio').value = u.bio||''; $('#pf-avatar-url').value = u.avatar||'';
  const unread = getNotifications().filter(n=>n.userId===u.id && !n.read).length;
  $('#nav-notif').textContent = unread;
  renderContacts();
}

function renderRoute(hash){
  if(!hash) hash = '#feed';
  const [route, param] = hash.split('/');
  switch(route){
    case '#feed': renderFeed(); break;
    case '#profile': renderProfile(param||session.current.id); break;
    case '#friends': renderFriends(); break;
    case '#messages': renderMessages(param); break;
    case '#notifications': renderNotifications(); break;
    case '#settings': renderSettings(); break;
    default: renderFeed();
  }
  // Highlight nav
  $$('.nav a').forEach(a=>{ a.classList.toggle('active', hash.startsWith(a.getAttribute('data-route'))) })
}

window.addEventListener('hashchange', ()=> renderRoute(location.hash));

function renderFeed(query=''){
  const main = $('#col-main');
  main.innerHTML = '';

  const composer = document.createElement('div');
  composer.className='composer';
  composer.innerHTML = `<div class="row" style="gap:10px"><img class="avatar" src="${session.current.avatar}"/><button class="btn space" onclick="openModal('modal-post')">What's on your mind, ${session.current.name.split(' ')[0]}?</button></div>`;
  main.appendChild(composer);

  let posts = getPosts();
  // Privacy filter
  posts = posts.filter(p=> canSeePost(session.current.id, p));
  if(query) posts = posts.filter(p=> (p.text||'').toLowerCase().includes(query) || (getUserById(p.userId)?.name||'').toLowerCase().includes(query));

  if(!posts.length){
    const empty = document.createElement('div');
    empty.className='panel'; empty.style.padding='20px';
    empty.innerHTML='<div class="center muted" style="height:140px">No posts yet. Be the first to share something!</div>';
    main.appendChild(empty); return;
  }

  posts.forEach(p=> main.appendChild(renderPost(p)));
}

function canSeePost(userId, post){
  if(post.privacy==='public') return true;
  if(post.privacy==='private') return post.userId===userId;
  // friends
  const u = getUserById(userId); return post.userId===userId || u.friends.includes(post.userId);
}

function renderPost(p){
  const u = getUserById(p.userId);
  const el = document.createElement('article'); el.className='post';
  const liked = p.likes.includes(session.current.id);
  el.innerHTML = `
    <div class="row" style="gap:10px">
      <img class="avatar" src="${u.avatar}"/>
      <div class="stack" style="min-width:0">
        <div style="font-weight:700; white-space:nowrap; text-overflow:ellipsis; overflow:hidden">${u.name} <span class="muted small">· ${timeAgo(p.createdAt)}</span></div>
        <div class="xs muted">${privacyIcon(p.privacy)} ${p.privacy}</div>
      </div>
      <div class="space"></div>
      ${p.userId===session.current.id? `<button class="btn ghost small" onclick="deletePost('${p.id}')">🗑️</button>`:''}
    </div>
    <div style="margin:10px 4px 8px; white-space:pre-wrap">${escapeHTML(p.text)}</div>
    ${p.image? `<div class="media"><img src="${p.image}"/></div>`:''}
    <div class="row actions" style="margin-top:8px">
      <button class="btn small ${liked?'warn':''}" onclick="toggleLike('${p.id}'); renderRoute('#feed')">❤️ ${p.likes.length}</button>
      <span class="chip">💬 ${p.comments.length}</span>
      <button class="btn ghost small" onclick="location.hash='#profile/${u.id}'">View profile</button>
    </div>
    <div class="sep"></div>
    <div class="stack">
      ${p.comments.map(c=> renderCommentHTML(c)).join('')}
      <div class="comment-box">
        <img class="avatar" src="${session.current.avatar}"/>
        <input placeholder="Write a comment" onkeydown="if(event.key==='Enter'){ addComment('${p.id}', this.value); this.value=''; renderRoute(location.hash) }"/>
      </div>
    </div>
  `;
  return el;
}

function renderCommentHTML(c){
  const u = getUserById(c.userId);
  return `<div class="row" style="gap:8px">
    <img class="avatar" src="${u.avatar}"/>
    <div class="stack" style="background:#0f1722; border:1px solid var(--border); padding:8px 10px; border-radius:12px; flex:1">
      <div class="small" style="font-weight:700">${u.name} <span class="muted">· ${timeAgo(c.createdAt)}</span></div>
      <div>${escapeHTML(c.text)}</div>
    </div>
  </div>`
}

function deletePost(id){
  const posts = getPosts().filter(p=>p.id!==id); setPosts(posts); toast('Post deleted'); renderRoute('#feed');
}

function renderProfile(userId){
  const main = $('#col-main'); main.innerHTML='';
  const u = getUserById(userId) || session.current;
  const me = session.current;
  const isMe = me.id===u.id;
  const header = document.createElement('div'); header.className='panel'; header.style.padding='16px';
  header.innerHTML = `
    <div class="row" style="gap:16px">
      <img class="avatar" style="width:72px; height:72px; border-radius:18px" src="${u.avatar}"/>
      <div class="stack space">
        <div style="font-size:20px; font-weight:800">${u.name}</div>
        <div class="muted small">@${u.username} · ${u.friends.length} friends</div>
        <div class="small">${escapeHTML(u.bio||'')}</div>
      </div>
      ${isMe? `<button class="btn" onclick="openModal('modal-profile')">Edit profile</button>` : renderFriendButtonHTML(u)}
    </div>`;
  main.appendChild(header);

  const posts = getPosts().filter(p=>p.userId===u.id && canSeePost(session.current.id, p));
  if(!posts.length){
    const empty = document.createElement('div'); empty.className='panel'; empty.style.padding='20px'; empty.innerHTML='<div class="center muted" style="height:120px">No posts yet.</div>'; main.appendChild(empty); return;
  }
  posts.forEach(p=> main.appendChild(renderPost(p)));
}

function renderFriendButtonHTML(u){
  const me = session.current; const pending = getFriendRequests().find(fr=>fr.from===me.id && fr.to===u.id && fr.status==='pending');
  const already = me.friends.includes(u.id);
  if(already) return `<button class="btn ghost">✓ Friends</button>`;
  if(pending) return `<button class="btn ghost" disabled>Pending...</button>`;
  return `<button class="btn" onclick="sendFriendRequest('${u.id}'); toast('Request sent');">Add friend</button>`
}

function renderFriends(){
  const main = $('#col-main'); main.innerHTML='';
  const u = session.current;

  const inbound = getFriendRequests().filter(fr=>fr.to===u.id && fr.status==='pending');
  const box = document.createElement('div'); box.className='panel'; box.style.padding='14px';
  box.innerHTML = `<div style="font-weight:800; margin-bottom:8px">Friend requests</div>` + (inbound.length? inbound.map(fr=>{
    const a = getUserById(fr.from);
    return `<div class="row" style="gap:10px; margin:8px 0">
      <img class="avatar" src="${a.avatar}"/>
      <div class="stack space"><div style="font-weight:700">${a.name}</div><div class="muted xs">@${a.username}</div></div>
      <button class="btn" onclick="respondFriendRequest('${fr.id}', true); renderRoute('#friends')">Accept</button>
      <button class="btn ghost" onclick="respondFriendRequest('${fr.id}', false); renderRoute('#friends')">Decline</button>
    </div>`
  }).join('') : '<div class="muted small">No requests.</div>');
  main.appendChild(box);

  const grid = document.createElement('div'); grid.className='panel'; grid.style.padding='14px';
  grid.innerHTML = `<div style="font-weight:800; margin-bottom:8px">People</div>` + getUsers().filter(x=>x.id!==u.id).map(x=>{
    const isFriend = u.friends.includes(x.id);
    return `<div class="row" style="gap:10px; margin:8px 0">
      <img class="avatar" src="${x.avatar}"/>
      <div class="stack space"><div style="font-weight:700">${x.name}</div><div class="muted xs">@${x.username}</div></div>
      ${isFriend? '<span class="chip">Friend</span>' : `<button class="btn small" onclick="sendFriendRequest('${x.id}'); toast('Request sent')">Add friend</button>`}
    </div>`
  }).join('');
  main.appendChild(grid);
}

function renderMessages(withUserId=null){
  const main = $('#col-main'); main.innerHTML='';
  const u = session.current;

  const wrap = document.createElement('div'); wrap.className='panel'; wrap.style.padding='12px';
  const peers = getUsers().filter(x=>x.id!==u.id);
  const peerOptions = peers.map(p=>`<option value="${p.id}" ${withUserId===p.id?'selected':''}>${p.name}</option>`).join('');
  const thread = getMessages().filter(m=> (m.from===u.id && m.to===withUserId) || (m.from===withUserId && m.to===u.id) );

  wrap.innerHTML = `
    <div class="row" style="gap:8px; margin-bottom:8px">
      <select id="msg-peer" class="chip"><option>Select person…</option>${peerOptions}</select>
      <div class="space"></div>
      <button class="btn ghost small" onclick="location.hash='#messages'">Clear</button>
    </div>
    <div id="msg-thread" style="min-height:200px; max-height:360px; overflow:auto; display:flex; flex-direction:column; gap:8px; padding:6px; background:#0b1219; border:1px solid var(--border); border-radius:12px; margin-bottom:8px"></div>
    <div class="row">
      <input id="msg-input" placeholder="Write a message" class="space"/>
      <button class="btn" id="msg-send">Send</button>
    </div>`;

  main.appendChild(wrap);

  const threadEl = $('#msg-thread');
  function paintThread(){
    const sel = $('#msg-peer').value; if(!sel || sel==='Select person…'){ threadEl.innerHTML = '<div class="center muted" style="height:160px">Choose someone to chat with.</div>'; return; }
    const t = getMessages().filter(m=> (m.from===u.id && m.to===sel) || (m.from===sel && m.to===u.id) );
    threadEl.innerHTML = t.map(m=>{
      const mine = m.from===u.id; const peer = getUserById(m.from);
      return `<div class="row" style="justify-content:${mine?'end':'start'}">
        <div style="max-width:70%; background:${mine?'#0b3b52':'#0f1722'}; border:1px solid var(--border); padding:8px 10px; border-radius:12px">${escapeHTML(m.text)}<div class="xs muted" style="margin-top:4px">${timeAgo(m.createdAt)}</div></div>
      </div>`
    }).join('');
    threadEl.scrollTop = threadEl.scrollHeight;
  }
  paintThread();

  $('#msg-peer').onchange = paintThread;
  $('#msg-send').onclick = ()=>{ const sel=$('#msg-peer').value; const txt=$('#msg-input').value.trim(); if(sel && txt){ sendMessage(sel, txt); $('#msg-input').value=''; paintThread(); }}
}

function renderNotifications(){
  const main = $('#col-main'); main.innerHTML='';
  const u = session.current;
  const list = getNotifications().filter(n=>n.userId===u.id);
  const box = document.createElement('div'); box.className='panel'; box.style.padding='12px';
  box.innerHTML = '<div style="font-weight:800; margin-bottom:8px">Notifications</div>' + (list.length? list.map(n=>{
    return `<div class="row" style="gap:10px; margin:6px 0">
      <div style="width:10px; height:10px; border-radius:999px; background:${n.read?'#334155':'#22c55e'}"></div>
      <div class="stack space"><div>${escapeHTML(n.text)}</div><div class="xs muted">${timeAgo(n.createdAt)}</div></div>
      ${n.data?.postId? `<button class="btn ghost xs" onclick="openPost('${n.data.postId}')">Open</button>`:''}
    </div>`
  }).join('') : '<div class="muted small">Nothing new.</div>');
  main.appendChild(box);
  // mark read
  list.forEach(n=>n.read=true); setNotifications(getNotifications()); updateSidebar();
}

function openPost(id){
  // naive: just navigate to feed and scroll
  location.hash = '#feed'; setTimeout(()=>{ const el = $(`article.post button[onclick*="${id}"]`)?.closest('article.post'); el?.scrollIntoView({behavior:'smooth', block:'center'}) }, 60);
}

function renderSettings(){
  const main = $('#col-main'); main.innerHTML='';
  const box = document.createElement('div'); box.className='panel'; box.style.padding='14px';
  box.innerHTML = `
    <div style="font-weight:800; margin-bottom:8px">Settings</div>
    <div class="stack">
      <div class="row" style="justify-content:space-between; gap:10px">
        <div>
          <div>Dark theme</div>
          <div class="hint">Always on in this demo 😉</div>
        </div>
        <label class="btn ghost">On</label>
      </div>
      <div class="sep"></div>
      <div class="row" style="justify-content:space-between; gap:10px">
        <div>
          <div>Export data</div>
          <div class="hint">Download your local demo data as JSON</div>
        </div>
        <button class="btn" onclick="exportData()">Export</button>
      </div>
      <div class="row" style="justify-content:space-between; gap:10px">
        <div>
          <div>Reset demo</div>
          <div class="hint">Clear localStorage and re-seed</div>
        </div>
        <button class="btn warn" onclick="resetDemo()">Reset</button>
      </div>
    </div>`;
  main.appendChild(box);
}

function renderContacts(){
  const list = $('#contacts-list'); if(!list) return;
  const me = session.current; const others = getUsers().filter(u=>u.id!==me.id);
  list.innerHTML = others.map(u=>`<a class="contact" href="#messages/${u.id}"><span class="status"></span><img class="avatar" src="${u.avatar}"/><div class="stack"><div style="font-weight:700">${u.name}</div><div class="xs muted">@${u.username}</div></div></a>`).join('');
}

// Auth screen
function renderAuth(){
  const root = document.getElementById('root');
  root.innerHTML = `
    <div class="auth">
      <div class="card two-col">
        <div>
          <div class="brand" style="font-size:26px; margin-bottom:10px"><div class="logo"><span>f</span></div> FaceLook</div>
          <p class="muted">A single-file, local-only social app. Create an account, post updates, like/comment, add friends, chat, and get notifications — all stored in your browser.</p>
          <div class="sep"></div>
          <div class="field"><label>Username</label><input id="li-username" placeholder="demo"/></div>
          <div class="field"><label>Password</label><input id="li-password" type="password" placeholder="(anything in demo)"/></div>
          <div class="row" style="gap:8px; margin-top:10px">
            <button class="btn primary" id="btn-login">Log in</button>
            <button class="btn" id="btn-quick">Quick demo</button>
          </div>
        </div>
        <div>
          <div style="font-weight:800; margin-bottom:8px">Create account</div>
          <div class="grid">
            <div class="field"><label>Display name</label><input id="su-name" placeholder="Jane Doe"/></div>
            <div class="field"><label>Username</label><input id="su-username" placeholder="jane"/></div>
            <div class="field"><label>Avatar URL (optional)</label><input id="su-avatar" placeholder="https://…"/></div>
            <div class="field"><label>Bio</label><textarea id="su-bio" rows="3" placeholder="About you"></textarea></div>
            <button class="btn" id="btn-signup">Sign up</button>
          </div>
          <p class="hint" style="margin-top:10px">Passwords are not implemented in this demo. Do not use real credentials.</p>
        </div>
      </div>
    </div>`;

  $('#btn-login').onclick = ()=>{
    const u = getUserByUsername($('#li-username').value.trim()) || getUserByUsername('demo');
    if(!u) return toast('User not found');
    session.currentUserId = u.id; renderApp(); toast('Welcome back!');
  }
  $('#btn-quick').onclick = ()=>{ const u=getUserByUsername('demo'); session.currentUserId=u.id; renderApp(); }
  $('#btn-signup').onclick = ()=>{
    const name = $('#su-name').value.trim(); const username=$('#su-username').value.trim();
    if(!name || !username) return toast('Name and username required');
    if(getUserByUsername(username)) return toast('Username taken');
    const user = { id:uid(), username, name, bio:$('#su-bio').value.trim(), avatar: $('#su-avatar').value.trim()||`https://picsum.photos/seed/${username}/200`, friends:[], createdAt: now() };
    setUsers([ ...getUsers(), user ]); session.currentUserId = user.id; toast('Account created'); renderApp();
  }
}

// Utilities
function timeAgo(iso){
  const s = Math.floor((Date.now()-new Date(iso).getTime())/1000);
  if(s<60) return `${s}s`;
  const m = Math.floor(s/60); if(m<60) return `${m}m`;
  const h = Math.floor(m/60); if(h<24) return `${h}h`;
  const d = Math.floor(h/24); if(d<30) return `${d}d`;
  const mo = Math.floor(d/30); if(mo<12) return `${mo}mo`;
  const y = Math.floor(mo/12); return `${y}y`;
}
function privacyIcon(p){ return p==='public'?'🌍':(p==='friends'?'👥':'🔒') }
function toast(msg){ const t=$('#toast'); t.textContent=msg; t.classList.remove('hidden'); setTimeout(()=>t.classList.add('hidden'), 1800) }
function openModal(id){ const d = document.getElementById(id); d.showModal() }
function closeModal(id){ const d = document.getElementById(id); d.close() }
function escapeHTML(str=''){ return str.replace(/[&<>"']/g, c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;','\'':'&#039;'}[c])) }
function exportData(){ const data = { users:getUsers(), posts:getPosts(), messages:getMessages(), friendRequests:getFriendRequests(), notifications:getNotifications() }; const blob = new Blob([JSON.stringify(data,null,2)], {type:'application/json'}); const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download='facelook-data.json'; a.click(); URL.revokeObjectURL(url); }
function resetDemo(){ localStorage.clear(); seed(); renderApp(); }

// Boot
seed();
if(session.current) renderApp(); else renderAuth();
</script>
</body>
</html>
