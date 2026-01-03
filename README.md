<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>OPEN WHEN LETTERS</title>

<script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: lavender;
  margin: 0;
  padding: 0;
  transition: background-color 1s;
}

h1 {
  text-align: center;
  color: plum;
  margin: 24px 0;
}

.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

.container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 24px;
  width: 100%;
  max-width: 900px;
}

.card {
  background: lavenderblush;
  width: 200px;
  padding: 12px;
  border-radius: 12px;
  text-align: center;
  box-shadow: 0 4px 16px rgba(0,0,0,0.12);
  transition: transform 0.3s;
}

.card:hover {
  transform: scale(1.05);
}

.card img {
  width: 100%;
  height: 180px;
  object-fit: cover;
  border-radius: 8px;
  transition: filter 0.3s;
}

.card button {
  margin-top: 10px;
  padding: 8px 16px;
  background-color: plum;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.modal {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.65);
  justify-content: center;
  align-items: center;
  z-index: 10;
}

.modal-content {
  background: white;
  padding: 24px;
  border-radius: 12px;
  max-width: 85%;
  position: relative;
}

.close-btn {
  position: absolute;
  top: 10px;
  right: 15px;
  font-size: 24px;
  cursor: pointer;
}

#letterBox {
  font-family: "Courier New", monospace;
  font-size: 1.05rem;
  line-height: 1.7;
  white-space: pre-wrap;
  min-height: 300px;
  color: #333;
}

.cursor {
  display: inline-block;
  width: 7px;
  background: #333;
  animation: blink 1s infinite;
}

@keyframes blink {
  0%,50% {opacity:1;}
  51%,100% {opacity:0;}
}

canvas {
  position: fixed !important;
  z-index: 9999 !important;
  pointer-events: none;
}

@media (max-width:768px) {
  .card { width: 90%; }
}
</style>
</head>

<body>

<div class="wrapper">
  <h1>-💌- OPEN WHEN MESSAGES -💌-</h1>
  <div class="container" id="container"></div>
</div>

<!-- MODAL -->
<div class="modal" id="modal">
  <div class="modal-content">
    <span class="close-btn" onclick="closeModal()">&times;</span>
    <div id="letterBox"></div>
  </div>
</div>

<script>
const recipes = [
  {id:0,name:"open when you get this",desc:"start here",img:"https://i.pinimg.com/736x/37/8e/c2/378ec2d0ec3528a0218cfce4277d1b5e.jpg",video:"https://player.vimeo.com/video/1151015697"},
  {id:1,name:"open when you are sad",desc:"its okay to feel like this",img:"https://i.pinimg.com/1200x/d7/52/2d/d7522d1a5953b0fae8e323d58710d5d1.jpg",video:"https://player.vimeo.com/video/1150406227"},
  {id:2,name:"open when you're lonely",desc:"never alone",img:"https://i.pinimg.com/736x/1b/27/3e/1b273ef50512d9e07fb9d0d23af3e12c.jpg",video:"https://player.vimeo.com/video/1150413574"},
  {id:3,name:"open when you need motivation",desc:"you got this",img:"https://i.pinimg.com/1200x/ce/31/30/ce31301bf40769ea5d70373efc83b9fc.jpg",video:"https://player.vimeo.com/video/1150404427"},
  {id:4,name:"open when you need a hug",desc:"tight hug",img:"https://i.pinimg.com/736x/1e/55/75/1e557575364a61ed4731fd2b126de168.jpg",video:"https://player.vimeo.com/video/1150634482"},
   {id:5,name:"open when you're afraid of failing 😟",desc:"Remember, failure is just a stepping stone to success  🌟",img:"https://i.pinimg.com/1200x/67/8a/ca/678aca6d77df8274cda5c1041757e8d5.jpg",video:"https://player.vimeo.com/video/115101100"},
  {id:6,name:"open when you feel nobody loves you 💘",desc:"i do",img:"https://i.pinimg.com/736x/11/56/89/115689278adfa6c0bf82ee8696d5d5a7.jpg",video:"https://player.vimeo.com/video/1151009847"},
  {id:7,name:"open when you had a bad day",desc:"A reminder that tomorrow is a new day.🌞",img:"https://i.pinimg.com/1200x/a5/d7/21/a5d72127150cf4f5dbc674d807ff9d83.jpg",video:"https://player.vimeo.com/video/1105513154"},
  {id:8,name:"open when you question your existence",desc:"You are here for a reason.🌈",img:"https://i.pinimg.com/1200x/dd/a4/a7/dda4a74103828d06df0ef077d9fd6887.jpg",video:"https://player.vimeo.com/video/1151010647"},
  {id:9,name:"open when you feel confused about your life decisionss",desc:"trust yourself and your journey",img:"https://i.pinimg.com/1200x/a4/a4/77/a4a477e8715305d88d46a6bdb5d0e81e.jpg",video:"https://player.vimeo.com/video/1151010337"},
  {id:10,name:"open whenever you feel like",desc:"bss yeh bhi add krne ka mann kiya",img:"https://i.pinimg.com/1200x/1c/83/55/1c835574b7926149349dd2e8bbbed05d.jpg",video:"https://player.vimeo.com/video/1150401518"},

  /* SECRET FINAL CARD */
  /* SECRET FINAL CARD */
  {
    id:11,
    name:"one last thing…",
    desc:"for the days ...you'll miss me....(not gonna happen tho :))",
    img:"https://i.pinimg.com/736x/40/e7/87/40e78770fdf6a528393ffa8b1dffd67e.jpg",
    video:"letter",
    secret:true
  }
];

const finalLetter = `
Hey Anshu-man,

If you’re reading this, it means we reached the end of something
that mattered to me.

I don’t regret us.
Not the laughs, not the late talks,
not even the difficult days.

I hope life is kind to you.
I hope you find happiness, even if it’s not with me.

And when you think of us,
I hope it’s with a small smile —
not pain.

I loved you in the only way I knew how.
And I believe you loved me too.

I want to see you achieve everything you've ever told me,
even if i'm not there..with you..
Take care..
Jai Baba Kiii :)
.
.
`;
const container = document.getElementById("container");
const modal = document.getElementById("modal");
const letterBox = document.getElementById("letterBox");

const unlocked = JSON.parse(localStorage.getItem("unlocked")) || [0];

render();

function render() {
  container.innerHTML = "";
  recipes.forEach(r => {
    if (r.secret && unlocked.length < recipes.length) return;

    const locked = !unlocked.includes(r.id) && !r.secret;

    const card = document.createElement("div");
    card.className = "card";
    card.innerHTML = `
      <img src="${r.img}" style="filter:${locked?'blur(4px)':'none'}">
      <h3>${r.name}</h3>
      <p>${locked?"🔒 keep going":r.desc}</p>
      <button ${locked?"disabled":""}
        onclick="openCard('${r.video}',this,${r.id})">
        ${locked?"Locked 🔐":"Open 💌"}
      </button>
    `;
    container.appendChild(card);
  });
}

function openCard(content, btn, id) {
  modal.style.display = "flex";
  letterBox.innerHTML = "";

  if (content === "letter") {
    document.body.style.backgroundColor = "#f1edf6";
    typeLetter(finalLetter);
  } else {
    document.body.style.backgroundColor = "lavender";
    letterBox.innerHTML =
      `<iframe src="${content}?autoplay=1"
        style="width:100%;height:400px;border:none;border-radius:8px;"></iframe>`;

    const r = btn.getBoundingClientRect();
    confetti({
      particleCount:80,
      spread:70,
      origin:{
        x:(r.left+r.width/2)/window.innerWidth,
        y:(r.top+r.height/2)/window.innerHeight
      }
    });
  }

  if (!unlocked.includes(id+1)) {
    unlocked.push(id+1);
    localStorage.setItem("unlocked",JSON.stringify(unlocked));
  }
}

function typeLetter(text) {
  let i = 0;
  const cursor = document.createElement("span");
  cursor.className = "cursor";
  letterBox.appendChild(cursor);

  function typeNext() {
    if (i < text.length) {
      cursor.insertAdjacentText("beforebegin", text[i]);

      if (text[i] === "\n") {
        i++;
        setTimeout(typeNext, 700); // pause after lines
        return;
      }
      i++;
      setTimeout(typeNext, 40);
    } else {
      cursor.remove(); // silence at the end
    }
  }
  typeNext();
}

function closeModal(){
  modal.style.display="none";
  document.body.style.backgroundColor = "lavender";
  render();
}
</script>

</body>
</html>
