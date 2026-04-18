<!DOCTYPE html>
<html>
<head>
  <title>Island Show Simulator</title>
  <style>
    body {
      font-family: Arial;
      background: #1e1e2f;
      color: white;
      text-align: center;
    }

    .container {
      display: flex;
      justify-content: space-around;
      margin-top: 20px;
    }

    .team, .log {
      width: 30%;
      background: #2e2e4f;
      padding: 10px;
      border-radius: 10px;
    }

    .player {
      display: flex;
      align-items: center;
      margin: 8px 0;
      background: #3a3a6a;
      padding: 5px;
      border-radius: 8px;
    }

    .player img {
      width: 50px;
      height: 50px;
      border-radius: 50%;
      margin-right: 10px;
    }

    button {
      margin-top: 15px;
      padding: 10px;
      border: none;
      border-radius: 5px;
      background: #4caf50;
      color: white;
      cursor: pointer;
    }
  </style>
</head>
<body>

<h1>🏝 Island Show Simulator</h1>
<p id="episode">Episode 1</p>

<button onclick="nextEpisode()">Next Episode ▶</button>

<div class="container">
  <div class="team">
    <h2>Team A</h2>
    <div id="teamA"></div>
  </div>

  <div class="team">
    <h2>Team B</h2>
    <div id="teamB"></div>
  </div>

  <div class="log">
    <h2>Episode Log</h2>
    <div id="log"></div>
  </div>
</div>

<script>
let episode = 1;
let merged = false;

// 🧍 Players with images
let players = [
  {
    name:"Alex",
    strength:8, social:5, intelligence:6,
    image:"https://i.pravatar.cc/100?img=1"
  },
  {
    name:"Jordan",
    strength:4, social:9, intelligence:7,
    image:"https://i.pravatar.cc/100?img=2"
  },
  {
    name:"Taylor",
    strength:6, social:6, intelligence:8,
    image:"https://i.pravatar.cc/100?img=3"
  },
  {
    name:"Sam",
    strength:7, social:4, intelligence:5,
    image:"https://i.pravatar.cc/100?img=4"
  },
  {
    name:"Riley",
    strength:5, social:8, intelligence:6,
    image:"https://i.pravatar.cc/100?img=5"
  },
  {
    name:"Casey",
    strength:6, social:5, intelligence:7,
    image:"https://i.pravatar.cc/100?img=6"
  }
];

let teamA = players.slice(0,3);
let teamB = players.slice(3);

function renderTeam(team, elementId) {
  document.getElementById(elementId).innerHTML =
    team.map(p => `
      <div class="player">
        <img src="${p.image}">
        <span>${p.name}</span>
      </div>
    `).join("");
}

function render() {
  renderTeam(teamA, "teamA");
  renderTeam(teamB, "teamB");
  document.getElementById("episode").innerText = "Episode " + episode;
}

function log(msg) {
  document.getElementById("log").innerHTML += `<p>${msg}</p>`;
}

function teamScore(team) {
  return team.reduce((s,p) =>
    s + p.strength + p.intelligence + Math.random()*3, 0);
}

function randomEvent() {
  let events = [
    "An argument breaks out!",
    "Two players form an alliance.",
    "Someone finds an advantage.",
    "Trust is broken.",
    "A secret plan is made."
  ];
  return events[Math.floor(Math.random()*events.length)];
}

function eliminate(team) {
  let weights = team.map(p => 10 - p.social + Math.random()*5);
  let total = weights.reduce((a,b)=>a+b,0);
  let pick = Math.random()*total;

  let index = 0;
  for (let i=0;i<team.length;i++) {
    pick -= weights[i];
    if (pick <= 0) {
      index = i;
      break;
    }
  }

  let out = team[index];
  log(out.name + " is eliminated!");
  team.splice(index,1);
}

function nextEpisode() {
  document.getElementById("log").innerHTML = "";

  log(randomEvent());

  if (!merged && playersLeft() <= 4) {
    merged = true;
    teamA = teamA.concat(teamB);
    teamB = [];
    log("🔥 Teams have merged!");
  }

  if (!merged) {
    let scoreA = teamScore(teamA);
    let scoreB = teamScore(teamB);

    let losingTeam = scoreA > scoreB ? teamB : teamA;

    log("One team loses the challenge!");
    eliminate(losingTeam);

  } else {
    log("Individual challenge!");

    let all = teamA;
    let winner = all[Math.floor(Math.random()*all.length)];
    log("🏆 " + winner.name + " wins immunity!");

    let eligible = all.filter(p => p !== winner);
    eliminate(eligible);

    teamA = teamA.filter(p => eligible.includes(p) || p === winner);
  }

  episode++;

  if (playersLeft() === 1) {
    let winner = teamA[0];
    document.body.innerHTML = `
      <h1>🏆 Winner: ${winner.name}</h1>
      <img src="${winner.image}" width="120" style="border-radius:50%">
    `;
    return;
  }

  render();
}

function playersLeft() {
  return teamA.length + teamB.length;
}

render();
</script>

</body>
</html>
