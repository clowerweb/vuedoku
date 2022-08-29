<script>
  import GameSettings from '@/components/GameSettings.vue';
  import SudokuGrid from '@/components/SudokuGrid.vue';
  import GameNumberPad from '@/components/GameNumberPad.vue';
  import GameControls from '@/components/GameControls.vue';

  export default {
    components: {GameSettings, SudokuGrid, GameNumberPad, GameControls},
    data() {
      return {
        locked: false,
        noteMode: false,
        shiftDown: false,
        hasErrors: false,
        gridState: [],
        openModals: [],
        actualSolvedSudoku: [],
        unsolvedSudoku: [],
        ns: 0,
      };
    },
    computed: {
      cellsWithValue() {
        const cellsWithValue = this.gridState.map(box => box.filter(
            obj => obj.val,
        ));

        return [].concat.apply([], cellsWithValue);
      },
    },
    mounted() {
      let puzzle;
      const params = new Proxy(new URLSearchParams(window.location.search), {
        get: (searchParams, prop) => searchParams.get(prop),
      });

      puzzle = params.puzzle;

      if (puzzle) {
        this.setPuzzle(puzzle);
      }

      document.addEventListener('keydown', (e) => {
        const numPadCodes = [];

        for (let i = 1; i < 10; i++) {
          numPadCodes.push(`Numpad${i}`);
        }

        if (e.key === 'Shift') {
          e.preventDefault();
          this.shiftDown = true;
          this.noteMode = true;
        }

        this.$refs.game.handleCellInput(e);
      });

      document.addEventListener('keyup', (e) => {
        if (e.key === 'Shift') {
          this.shiftDown = false;
          this.noteMode = false;
        }
      });
    },
    methods: {
      setPuzzle(puzzle) {
        const newState = puzzle.split('');
        const chunkSize = 9;
        const chunks = [];

        for (let i = 0; i < newState.length; i += chunkSize) {
          chunks.push(newState.slice(i, i + chunkSize));
        }

        if (newState.length === 81) {
          for (let i = 0; i < chunks.length; i++) {
            const gridRow = this.gridState.map(box => {
              return box.filter(cell => cell.position.row === i);
            });
            const row = [].concat.apply([], gridRow);

            for (let x = 0; x < chunks[i].length; x++) {
              const val = chunks[i][x];

              if (val.match(/[1-9]/)) {
                row[x].val = parseInt(val);
              }
            }
          }

          this.$refs.game.checkErrors();
          this.hasErrors = this.$refs.game.hasErrors;

          if (!this.hasErrors) {
            const cellsWithValue = this.gridState.map(box => box.filter(
                obj => obj.val,
            ));
            const cells = [].concat.apply([], cellsWithValue);

            for (const cell of cells) {
              cell.locked = true;
            }

            this.lock();
            this.$refs.game.handleStateChange();
          }
        }
      },
      handleStateChange(state) {
        this.gridState = state;
      },
      setNumber(num) {
        this.$refs.game.handleCellInput({code: `Digit${num}`});
      },
      lock() {
        this.locked = true;
        this.$refs.game.toggleLock(true);
      },
      toggleLock() {
        this.locked = !this.locked;
        this.$refs.game.toggleLock(this.locked);
      },
      toggleNotes() {
        this.noteMode = !this.noteMode;
      },
      quickNotes() {
        this.$refs.game.quickNotes();
      },
      erase() {
        this.$refs.game.handleCellInput({code: 'Delete'});
      },
      undo() {
        //
      },
      redo() {
        //
      },
      hint() {
        //
      },
      clearNotes() {
        this.$refs.game.resetNotes();
        this.closeModal('clear-modal');
      },
      clearUnlocked() {
        this.$refs.game.resetUnlocked();
        this.closeModal('clear-modal');
      },
      clearNotesAndUnlocked() {
        this.$refs.game.resetNotesAndUnlocked();
        this.closeModal('clear-modal');
      },
      clearAll() {
        this.$refs.game.resetBoard();
        this.closeModal('clear-modal');
      },
      openModal(modalId) {
        const idx = this.openModals.indexOf(modalId);

        if (idx === -1) {
          this.openModals.push(modalId);
        }
      },
      closeModal(modalId) {
        const idx = this.openModals.indexOf(modalId);

        if (idx > -1) {
          this.openModals.splice(idx, 1);
        }
      },
      generate(difficulty) {
        const difficulties = { easy: 62, medium: 47, hard: 36, expert: 27 };
        let givens = difficulties[difficulty];

        for(let k=0; k<9; ++k) {
          this.actualSolvedSudoku[k] = [];
          this.unsolvedSudoku[k] = [];
        }

        this.generatePrincipalDiagonals();
        this.solveSudoku();
        this.generateEmptySudoku(givens);
      },
      generatePrincipalDiagonals() {
        this.generateEachPrincipalDiagonal(0,0);
        this.generateEachPrincipalDiagonal(3,3);
        this.generateEachPrincipalDiagonal(6,6);
      },
      generateEachPrincipalDiagonal(i, j) {
        for (let a = 0; a < 3; a++) {
          for (let b = 0; b < 3; b++) {
            let check = true;

            while (check) {
              let n = Math.floor(Math.random() * 10 + 1) % 10;

              if (n === 0) n = 1;

              if (this.isValidInDiagonal(n, i, j)) {
                this.actualSolvedSudoku[a + i][b + j] = Math.floor(n);
                check = false;
              }
            }
          }
        }
      },
      isValidInDiagonal(n, i, j) {
        for (let a = 0; a < 3; a++) {
          for (let b = 0; b < 3; b++) {
            if (this.actualSolvedSudoku[a + i][b + j] === n) {
              return false;
            }
          }
        }

        return true;
      },
      solveSudoku() {
        this.solve(this.actualSolvedSudoku, 0, 0);
      },
      solve(board, i, j) {
        i += Math.floor(j / 9);
        j = j % 9;

        if (i===9) return true;

        if(board[i][j] !== undefined) {
          return this.solve(board, i, j + 1);
        }

        for (let val = 1; val <= 9; ++val)
          if (this.isValid(board, i, j, val)) {
            board[i][j]=val;

            if(this.solve(board, i, j + 1)) return true;

            board[i][j] = undefined;
          }

        return false;
      },
      isValid(board, row, col, val) {
        //check row
        for (let i = 0; i <= 8; ++i) {
          if (board[row][i] !== undefined) {
            if (board[row][i] === val) return false;
          }
        }

        //check column
        for (let i = 0; i <= 8; ++i) {
          if (board[i][col] !== undefined) {
            if (board[i][col] === val) return false;
          }
        }

        //check grid
        for (let i = 0; i <= 2; ++i)
          for (let j = 0; j <= 2; ++j) {
            if(board[Math.floor(row / 3) * 3 + i][Math.floor(col / 3) * 3 + j] !== undefined) {
              if(board[Math.floor(row / 3) * 3 + i][Math.floor(col / 3) * 3 + j] === val) {
                return false;
              }
            }
          }

        return true;
      },
      generateEmptySudoku(givens) {
        for (let i = 0; i < 9; ++i) {
          for (let j = 0; j < 9; ++j) {
            this.unsolvedSudoku[i][j] = this.actualSolvedSudoku[i][j];
          }
        }

        let emp = 81 - givens;
        let i = 0;

        while(i < emp) {
          const ran_r = Math.floor(Math.random() * 10) % 9;
          const ran_c = Math.floor(Math.random() * 10) % 9;

          if (this.unsolvedSudoku[ran_r][ran_c] === 0) continue;

          this.ns=0;
          this.unsolvedSudoku[ran_r][ran_c] = 0;

          this.checkValidSudoku(this.unsolvedSudoku, 0, 0);

          if (this.ns !== 1) {
            this.unsolvedSudoku[ran_r][ran_c] = this.actualSolvedSudoku[ran_r][ran_c];
          } else {
            ++i;
          }
        }
      },
      checkValidSudoku(board, i, j) {
        i += Math.floor(j / 9);
        j = j % 9;

        if (i === 9) return true;

        if(board[i][j] !== 0) {
          return this.checkValidSudoku(board, i, j + 1);
        }

        for(let val = 1; val <= 9; ++val)
          if(this.isValid(board, i, j, val)) {
            board[i][j] = val;

            if(this.checkValidSudoku(board, i, j+1)) ++this.ns;

            board[i][j] = 0;
          }

        return false;
      },
      newGame(difficulty) {
        this.$refs.game.resetBoard();
        this.openModals = [];
        this.generate(difficulty);
        this.setPuzzle(this.unsolvedSudoku.map(a => a.join('')).join(''));
      },
    },
  };
</script>

<template>
  <article>
    <header>
      <GameSettings
        :open-modals="openModals"
        @open="openModal('settings-modal')"
        @close-modal="closeModal"
        @new-game="newGame"
      ></GameSettings>
    </header>

    <main>
      <SudokuGrid
        ref="game"
        @lock-puzzle="lock"
        @state-change="handleStateChange"
        :note-mode="noteMode"
        :shift-down="shiftDown"
        :cells-with-value="cellsWithValue"
      ></SudokuGrid>
    </main>

    <footer>
      <GameNumberPad
        @setNumber="setNumber"
        :cells-with-value="cellsWithValue"
      ></GameNumberPad>

      <GameControls
        @toggleLock="toggleLock"
        @toggleNotes="toggleNotes"
        @quickNotes="quickNotes"
        @erase="erase"
        @undo="undo"
        @redo="redo"
        @hint="hint"
        @solve="solve"
        @clear="openModal('clear-modal')"
        @close-modal="closeModal"
        @clear-notes="clearNotes"
        @clear-unlocked="clearUnlocked"
        @clear-notes-unlocked="clearNotesAndUnlocked"
        @clear-all="clearAll"
        :locked="locked"
        :note-mode="noteMode"
        :open-modals="openModals"
      ></GameControls>
    </footer>
  </article>
</template>

<style scoped lang="scss">
  article {
    display: flex;
    flex-direction: column;
    max-width: 100%;
    margin: 0 auto;
    width: 600px;

    footer {
      position: relative;
      z-index: 99;
    }
  }
</style>
