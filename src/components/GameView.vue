<script>
	import SudokuGrid from '@/components/SudokuGrid.vue';
	import GameNumberPad from '@/components/GameNumberPad.vue';
	import GameControls from '@/components/GameControls.vue';

	export default {
		components: { SudokuGrid, GameNumberPad, GameControls },
		data() {
			return {
				locked: false,
				noteMode: false,
				shiftDown: false,
				gridState: []
			};
		},
		computed: {
			cellsWithValue() {
				const cellsWithValue = this.gridState.map(box => box.filter(
						obj => obj.val
				));

				return [].concat.apply([], cellsWithValue);
			},
		},
		mounted() {
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
			handleStateChange(state) {
				this.gridState = state;
			},
			setNumber(num) {
				this.$refs.game.handleCellInput({ code: `Digit${num}` });
				this.$refs.game.focusActiveCellInput();
			},
			toggleLock() {
				this.locked = !this.locked;
				this.$refs.game.toggleLock(this.locked);
				this.$refs.game.focusActiveCellInput();
			},
			toggleNotes() {
				this.noteMode = !this.noteMode;
				this.$refs.game.focusActiveCellInput();
			},
			quickNotes() {
				this.$refs.game.quickNotes();
				this.$refs.game.focusActiveCellInput();
			},
			erase() {
				this.$refs.game.handleCellInput({ code: 'Delete' });
				this.$refs.game.focusActiveCellInput();
			},
			undo() {
				this.$refs.game.focusActiveCellInput();
			},
			redo() {
				this.$refs.game.focusActiveCellInput();
			},
			hint() {
				this.$refs.game.focusActiveCellInput();
			},
			solve() {
				this.$refs.game.focusActiveCellInput();
			},
			clearAll() {
				this.$refs.game.resetBoard();
			}
		}
	};
</script>

<template>
	<article>
		<main>
			<SudokuGrid
				ref="game"
				@stateChange="handleStateChange"
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
				@clearAll="clearAll"
				:locked="locked"
				:note-mode="noteMode"
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
