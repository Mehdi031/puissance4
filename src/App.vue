<script setup>
import { ref, reactive } from 'vue'

let joueur = ref('chat')

const tableau = reactive([
  ['', '', '', '', '', '', ''],
  ['', '', '', '', '', '', ''],
  ['', '', '', '', '', '', ''],
  ['', '', '', '', '', '', ''],
  ['', '', '', '', '', '', ''],
  ['', '', '', '', '', '', ''],
])

let positionPiece = ref(0)

function mouvement_tableau(event) {
  positionPiece.value = event.pageX - 30
}

function click_td(ligne, colonne) {
  let ligneLibre = false
  for (let l = 5; l >= 0; l--) {
    if (tableau[l][colonne] === '') {
      ligneLibre = l
      break
    }
  }
  if (ligneLibre === false) return

  tableau[ligneLibre][colonne] = joueur.value

  if (verifier_gagner(joueur.value, ligneLibre, colonne)) {
    const gagnant = joueur.value
    setTimeout(() => alert(gagnant + ' a gagné'), 550)
  }

  joueur.value = joueur.value === 'chat' ? 'souris' : 'chat'
}

function verifier_gagner(j, ligne, colonne) {
  for (const [dx, dy] of [
    [0, 1],
    [1, 0],
    [1, 1],
    [1, -1],
  ]) {
    let x = colonne
    let y = ligne

    while (
      x - dx >= 0 &&
      y - dy >= 0 &&
      y - dy < 6 &&
      tableau[y - dy][x - dx] === j
    ) {
      x -= dx
      y -= dy
    }

    let count = 0
    while (
      x < 7 &&
      y < 6 &&
      y >= 0 &&
      tableau[y][x] === j
    ) {
      count++
      x += dx
      y += dy
    }

    if (count >= 4) return true
  }
  return false
}

function rejouer() {
  for (let l = 0; l < 6; l++) {
    for (let c = 0; c < 7; c++) {
      tableau[l][c] = ''
    }
  }
  joueur.value = 'chat'
}
</script>

<template>
  <div id="jeu">
    <div id="joueur-chat" :class="{ joueur: true, actif: joueur === 'chat' }">
      <h2>Chat</h2>
      <img src="./assets/piece-chat.svg" />
    </div>

    <img
      id="piece-active"
      :style="'left:' + positionPiece + 'px'"
      :src="'/src/assets/piece-' + joueur + '.svg'"
    />

    <table @mousemove="mouvement_tableau">
      <tr v-for="ligne in 6">
        <td v-for="colonne in 7" @click="click_td(ligne - 1, colonne - 1)">
          <Transition name="trans-piece">
            <img
              v-if="tableau[ligne - 1][colonne - 1] !== ''"
              :src="'/src/assets/piece-' + tableau[ligne - 1][colonne - 1] + '.svg'"
            />
          </Transition>
        </td>
      </tr>
    </table>

    <div id="joueur-souris" :class="{ joueur: true, actif: joueur === 'souris' }">
      <h2>Souris</h2>
      <img src="./assets/piece-souris.svg" />
    </div>
  </div>

  <!-- BOUTON REJOUER -->
  <div style="text-align: center; margin-top: 20px;">
    <button @click="rejouer">🔁 Rejouer</button>
  </div>
</template>

<style scoped>
#jeu {
  display: flex;
  margin-top: 100px;
}

#piece-active {
  position: absolute;
  left: 100px;
  top: 20px;
}

.joueur {
  width: 100px;
  height: 200px;
  margin: 10px;
  margin-top: 100px;
  background-color: blue;
  color: white;
  border-radius: 10px;
  font-family: sans;
  padding: 0.5em;
  text-align: center;
  box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.5);
}

.joueur.actif {
  background-color: #55f;
}

.joueur h2 {
  font-size: 18px;
}

table {
  padding: 25px;
  padding-top: 27px;
  padding-bottom: 23px;
  background-image: url('/src/assets/cadre.svg');
  background-size: cover;
  border-collapse: separate;
  border-spacing: 0;
}

td {
  text-align: center;
  width: 68px;
  height: 68px;
}

table img {
  position: relative;
  transition: top 0.5s ease-in;
  top: 0;
  z-index: -1;
}

#jeu tr:nth-child(1) .trans-piece-enter-from {
  top: -110px;
}
#jeu tr:nth-child(2) .trans-piece-enter-from {
  top: -180px;
}
#jeu tr:nth-child(3) .trans-piece-enter-from {
  top: -250px;
}
#jeu tr:nth-child(4) .trans-piece-enter-from {
  top: -320px;
}
#jeu tr:nth-child(5) .trans-piece-enter-from {
  top: -390px;
}
#jeu tr:nth-child(6) .trans-piece-enter-from {
  top: -460px;
}
</style>

