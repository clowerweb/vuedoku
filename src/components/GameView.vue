<script>
	import GameSettings from '@/components/GameSettings.vue';
	import SudokuGrid from '@/components/SudokuGrid.vue';
	import GameNumberPad from '@/components/GameNumberPad.vue';
	import GameControls from '@/components/GameControls.vue';

	export default {
		components: { GameSettings, SudokuGrid, GameNumberPad, GameControls },
		data() {
			return {
				locked: false,
				noteMode: false,
				shiftDown: false,
				gridState: [],
				openModals: [],
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
			lock() {
				this.locked = true;
				this.$refs.game.toggleLock(true);
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
			modalAction(action) {
				this[action]();
			},
		}
	};
</script>

<template>
	<article>
		<header>
			<GameSettings></GameSettings>
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
				@clear-unlocked="clearNotes"
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
