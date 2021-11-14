<script>
  import Field from "./Field.svelte";

  let winCombos = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

  let arr = [];
  let usersTurn = true;
  let message = "";

  const winner = (perc) => {
    if (arr.length >= 5) {
      let arr2 = arr.filter((val, i) => i % 2 === perc);

      winCombos.forEach((array) => {
        if (array.every((element) => arr2.includes(element))) {
          if (perc === 0) {
            message = "You won!";
            const game = document.querySelector(".game-frame");

            array.forEach((arr, index) => {
              game.childNodes[arr].style.backgroundColor = "#58D68D";
            });
          } else {
            message = "Computer won!";
            const game = document.querySelector(".game-frame");

            array.forEach((arr, index) => {
              game.childNodes[arr].style.backgroundColor = "#EC7063";
            });
          }
        }
      });
    }
  };

  const userMove = (e) => {
    if (e.target.firstChild.innerHTML === "" && !message && usersTurn) {
      e.target.firstChild.innerHTML = "X";
      arr.push(parseInt(e.target.getAttribute("number")));
      winner(0);
      usersTurn = false;

      if (arr.length !== 9) {
        pcMove();
      }
      if (arr.length === 9 && !message) {
        message = "A tie!";
      }
    }
  };

  const pcMove = () => {
    if (!message) {
      setTimeout(() => {
        let num;
        do {
          num = Math.floor(Math.random() * 9);
        } while (arr.includes(num));

        let arr2 = arr.filter((val, i) => i % 2 === 0);
        let arr3 = arr.filter((val, i) => i % 2 === 1);

        winCombos.forEach((array) => {
          if (
            array.some((element) => arr2.includes(element)) &&
            !array.some((element) => arr3.includes(element))
          ) {

            const findCommon = (a, b) => a.filter((n) => b.indexOf(n) !== -1);
            const common = findCommon(array, arr2);

            const findMissing = (a, b) => a.filter((n) => b.indexOf(n) === -1);
            const missing = findMissing(array, common);

            if (common.length === 2) {
              if (!arr.includes(missing)) {
                num = missing[0];
              }
            } else if (common.length === 1) {
            }
          }
        });

        const game = document.querySelector(".game-frame");
        game.childNodes[num].firstChild.innerHTML = "O";

        arr.push(num);
        usersTurn = true;
        winner(1);
      }, 500);
    }
  };

  const reset = () => {
    message = "";
    arr = [];
    usersTurn = true;

    const game = document.querySelector(".game-frame").childNodes;

    game.forEach((el) => {
      el.style.backgroundColor = "";
      el.firstChild.innerHTML = "";
    });
  };
</script>

<style>
  body {
    height: 100vh;
  }
  main {
    text-align: center;
    padding: 1em;
    max-width: 100%;
    margin: 0 auto;
    height: 100%;
  }

  .game-frame {
    max-width: 500px;
    min-height: 500px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    gap: 10px;
  }

  .title {
    color: white;
    margin-top: 20px;
    margin-bottom: 30px;
    font-size: 46px;
  }

  .reset {
    color: white;
    margin-top: 20px;
    font-size: 26px;
    cursor: pointer;
  }

  .footer {
    color: white;
    margin-top: 20px;
    margin-bottom: 30px;
    font-size: 14px;
    position: absolute;
    bottom: 10px;
    left: 0;
    right: 0;
  }

  .footer a {
    color: #eee;
  }

  .heart {
    fill: red;
    position: relative;
    top: 3px;
    width: 1rem;
    animation: pulse 1s ease infinite,
  }

  @keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.3); }
    100% { transform: scale(1); }
  }
</style>

<main>
  <h1 class="title">{!message ? 'Tic Tac Toe' : message}</h1>
  <div class="game-frame">
    {#each Array(9) as field, index}
      <Field onClick={userMove} number={index} />
    {/each}
  </div>
  <h1 class="reset" on:click={reset}>{!message ? 'Reset' : 'Play Again'}</h1>
  <div class="footer">
    <p>Powered by SystemPro. Made with
      <svg class="heart" viewBox="0 0 32 29.6">
        <path d="M23.6,0c-3.4,0-6.3,2.7-7.6,5.6C14.7,2.7,11.8,0,8.4,0C3.8,0,0,3.8,0,8.4c0,9.4,9.5,11.9,16,21.2
	c6.1-9.3,16-12.1,16-21.2C32,3.8,28.2,0,23.6,0z"/>
      </svg>
      by <a href="https://github.com/ZokiPokiDev" target="_blank">ZokiPokiDev</a></p>
  </div>
</main>
