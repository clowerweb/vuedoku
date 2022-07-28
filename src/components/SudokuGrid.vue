<script>
	export default {
		props: {
			noteMode: {
				type: Boolean,
				required: true,
				default: false
			},
			cellsWithValue: {
				type: Array,
				required: true
			}
		},
		data() {
			return {
				gridState: [],
				cellsWithVal: []
			};
		},
		created() {
			let boxRow = 0;
			let boxCol = 0;

			// Build the 9 boxes
			for (let i = 0; i < 9; i++) {
				let row = boxRow * 3;
				let col = boxCol * 3;
				const box = [];

				// Put 9 cells in each box
				for (let k = 0; k < 9; k++) {
					box.push({
						id: 9 * i + k,
						val: null,
						active: i === 0 && k === 0,
						locked: false,
						error: false,
						notes: [],
						position: {
							box: i,
							cell: k,
							row: row,
							col: col
						}
					});

					// Increment row
					if ((k + 1) % 3 === 0) row++;

					// Increment col
					col++;
					// Reset col
					if ((k + 1) % 3 === 0) col = boxCol * 3;
				}

				// Increment boxRow
				if ((i + 1) % 3 === 0) boxRow++;

				// Increment boxCol
				boxCol++;
				// Reset boxCol
				if ((i + 1) % 3 === 0) boxCol = 0;

				this.gridState.push(box);
			}
		},
		mounted() {
			this.focusActiveCellInput();
			this.handleStateChange();

			document.addEventListener('keypress', () => {
				this.focusActiveCellInput();
			});
		},
		computed: {
			/**
			 * Returns the currently active (last focused) cell
			 */
			activeCell() {
				const filtered = this.gridState.map((box) => {
					return box.filter(cell => cell.active);
				});
				const result = filtered.filter(result => result.length);

				return this.filterGridCells(result)[0] ?? [];
			},
			/**
			 * Returns all cells in the same box as the active cell
			 */
			cellsInBox() {
				const results = this.gridState.map(box => box.filter(
						obj => obj.position.box === this.activeCell?.position?.box
				));

				return this.filterGridCells(results);
			},
			/**
			 * Returns all cells in the same row as the active cell
			 */
			cellsInRow() {
				const results = this.gridState.map(box => box.filter(
						obj => obj.position.row === this.activeCell?.position?.row
				));

				return this.filterGridCells(results);
			},
			/**
			 * Returns all cells in the same column as the active cell
			 */
			cellsInCol() {
				const results = this.gridState.map(box => box.filter(
						obj => obj.position.col === this.activeCell?.position?.col
				));

				return this.filterGridCells(results);
			},
			/**
			 * Returns all cells in range (same box, row, and/or column) of the active cell
			 */
			cellsInRange() {
				const results = [];

				// Cells in the same box
				for (const boxCell of this.cellsInBox) {
					if (boxCell.id !== this.activeCell.id) {
						results.push(boxCell);
					}
				}

				// Cells in the same row
				for (const rowCell of this.cellsInRow) {
					if (rowCell.id !== this.activeCell.id) {
						results.push(rowCell);
					}
				}

				// Cells in the same column
				for (const colCell of this.cellsInCol) {
					if (colCell.id !== this.activeCell.id) {
						results.push(colCell);
					}
				}

				return results;
			},
			/**
			 * Returns all cells with the same value as the active cell
			 */
			cellsWithActiveValue() {
				const results = [];

				if (this.activeCell.val) {
					const filtered = this.gridState.map(box => box.filter(
							obj => obj.val === this.activeCell.val
					));
					const cleaned = this.filterGridCells(filtered);

					for (const cell of cleaned) {
						if (cell.id !== this.activeCell.id) {
							results.push(cell);
						}
					}
				}

				return results;
			},
			/**
			 * Returns all cells in range of any cells that have the same value as the active cell
			 */
			matchedInRange() {
				let results = [];

				for (const cell of this.cellsWithActiveValue) {
					// Cells in the same box
					const boxCellsFilter = this.gridState.map(box => box.filter(
							obj => obj.position.box === cell.position.box
					));
					const boxCells = this.filterGridCells(boxCellsFilter);

					for (const boxCell of boxCells) {
						if (boxCell.id !== cell.id) {
							results.push(boxCell);
						}
					}

					// Cells in the same row
					const rowCellsFilter = this.gridState.map(box => box.filter(
							obj => obj.position.row === cell.position.row
					));
					const rowCells = this.filterGridCells(rowCellsFilter);

					for (const rowCell of rowCells) {
						if (rowCell.id !== cell.id) {
							results.push(rowCell);
						}
					}

					// Cells in the same column
					const colCellsFilter = this.gridState.map(box => box.filter(
							obj => obj.position.col === cell.position.col
					));
					const colCells = this.filterGridCells(colCellsFilter);

					for (const colCell of colCells) {
						if (colCell.id !== cell.id) {
							results.push(colCell);
						}
					}
				}

				if (results.length) {
					results = results.concat(this.cellsInRange);
				}

				return results;
			},
			nextCellInRow() {
				const cellsAhead = this.cellsInRow.filter(cell => cell.id > this.activeCell.id);
				return cellsAhead.length ? cellsAhead[0] : this.cellsInRow[0];
			},
			previousCellInRow() {
				const cellsBehind = this.cellsInRow.filter(cell => cell.id < this.activeCell.id);
				return cellsBehind.length ? cellsBehind[cellsBehind.length - 1] : this.cellsInRow[8];
			},
			nextCellInCol() {
				const cellsAhead = this.cellsInCol.filter(cell => cell.id > this.activeCell.id);
				return cellsAhead.length ? cellsAhead[0] : this.cellsInCol[0];
			},
			previousCellInCol() {
				const cellsBehind = this.cellsInCol.filter(cell => cell.id < this.activeCell.id);
				return cellsBehind.length ? cellsBehind[cellsBehind.length - 1] : this.cellsInCol[8];
			},
			/*nextCellInBox() {

			},
			previousCellInBox() {

			}*/
		},
		methods: {
			handleStateChange() {
				this.$emit('state-change', this.gridState);
			},
			setGridState(state) {
				this.gridState = state;
			},
			resetBoard() {
				for (const box of this.gridState) {
					for (const cell of box) {
						cell.val = null;
						cell.notes = [];
						cell.locked = false;
					}
				}

				this.$emit('state-change', this.gridState);
			},
			/**
			 * Handle the active state of cells when one is focused
			 */
			handleCellFocus(cell) {
				if (this.activeCell?.id !== cell.id) {
					if (this.activeCell) {
						this.activeCell.active = false;
					}

					cell.active = true;
				}
			},
			/**
			 * Set the value of a cell
			 */
			handleCellInput(e) {
				if (!this.activeCell.locked) {
					let key = e.code;

					switch (key) {
						case 'Digit1':
						case 'Numpad1':
							key = 1;
							break;
						case 'Digit2':
						case 'Numpad2':
							key = 2;
							break;
						case 'Digit3':
						case 'Numpad3':
							key = 3;
							break;
						case 'Digit4':
						case 'Numpad4':
							key = 4;
							break;
						case 'Digit5':
						case 'Numpad5':
							key = 5;
							break;
						case 'Digit6':
						case 'Numpad6':
							key = 6;
							break;
						case 'Digit7':
						case 'Numpad7':
							key = 7;
							break;
						case 'Digit8':
						case 'Numpad8':
							key = 8;
							break;
						case 'Digit9':
						case 'Numpad9':
							key = 9;
							break;
						case 'Backspace':
						case 'Delete':
							key = 'Delete';
							break;
						default:
							key = null;
					}

					if (key !== 'Delete') {
						const numPadKeyCodes = [35, 40, 34, 37, 12, 39, 36, 38, 33];
						const numPadCodes = [];
						let input = parseInt(key);
						let noteMode = this.noteMode;

						for (let i = 1; i < 10; i++) {
							numPadCodes.push(`Numpad${i}`);
						}

						if(numPadKeyCodes.includes(e.keyCode) && numPadCodes.includes(e.code)) {
							noteMode = true;
						}

						if (input > 0 && input < 10) {
							if (!noteMode) {
								this.activeCell.notes = [];
								this.activeCell.val = input;
								this.checkErrors();

								for (const cell of this.cellsInRange) {
									const idx = cell.notes.indexOf(input);

									if (idx > -1 && !this.activeCell.error) {
										cell.notes.splice(cell.notes.indexOf(input), 1);
									}
								}
							} else {
								this.handleNotes(input);
							}

							this.handleStateChange();
						}

						switch (e.code) {
							case 'ArrowLeft':
								this.handleCellFocus(this.previousCellInRow);
								break;
							case 'ArrowRight':
								this.handleCellFocus(this.nextCellInRow);
								break;
							case 'ArrowUp':
								this.handleCellFocus(this.previousCellInCol);
								break;
							case 'ArrowDown':
								this.handleCellFocus(this.nextCellInCol);
								break;
						}
					} else {
						this.activeCell.val = null;
						this.activeCell.notes = [];
						this.handleStateChange();
						this.checkErrors();
					}
				} else {
					this.preventEvent(e);
				}

				return false;
			},
			handleNotes(input) {
				if (!this.activeCell.val) {
					const existingNotes = this.activeCell.notes;
					const existingIndex = existingNotes.indexOf(input);
					const valsInRange = this.filterGridCells(this.cellsInRange.filter(cell => cell.val === input));

					if (existingIndex === -1 && !valsInRange.length) {
						existingNotes.push(input);
					} else if (existingIndex > -1) {
						existingNotes.splice(existingIndex, 1);
					}
				}
			},
			/**
			 * Removes all of the empty arrays from a filtered result
			 */
			filterGridCells(cells) {
				return [].concat.apply([], cells);
			},
			/**
			 * Gets the class(es) a cell should have
			 */
			getCellClass(cell) {
				let cellClass = this.activeCell?.id === cell.id ? 'active' : '';
				cellClass += this.cellsInRange.includes(cell) ? ' in-range' : '';
				cellClass += this.cellsWithActiveValue.includes(cell) ? ' matched' : '';
				cellClass += this.matchedInRange.includes(cell) ? ' matched-in-range' : '';
				cellClass += cell.error ? ' error' : '';
				cellClass += cell.locked ? ' locked' : '';

				return cellClass;
			},
			getNotesClass(cell, i) {
				let notesClass = cell.notes.includes(i) ? 'active' : '';
				notesClass += this.activeCell.val === i ? ' matched' : '';

				return notesClass;
			},
			getCellsInPositionOf(position, cell) {
				const results = this.gridState.map(box => box.filter(
						obj => obj.position[position] === cell.position[position]
				));

				return this.filterGridCells(results);
			},
			getCellsInRangeOf(cell) {
				const inBox = this.getCellsInPositionOf('box', cell);
				const inRow = this.getCellsInPositionOf('row', cell);
				const inCol = this.getCellsInPositionOf('col', cell);
				const results = [];

				// Cells in the same box
				for (const boxCell of inBox) {
					if (boxCell.id !== cell.id) {
						results.push(boxCell);
					}
				}

				// Cells in the same row
				for (const rowCell of inRow) {
					if (rowCell.id !== cell.id) {
						results.push(rowCell);
					}
				}

				// Cells in the same column
				for (const colCell of inCol) {
					if (colCell.id !== cell.id) {
						results.push(colCell);
					}
				}

				return results;
			},
			checkErrors() {
				this.removeErrors();

				for (const box of this.gridState) {
					for (const cell of box) {
						const cellsInRange = this.getCellsInRangeOf(cell);
						const matchedVals = cellsInRange.filter(obj => obj.val === cell.val);

						if (matchedVals.length && cell.val) {
							for (const match of matchedVals) {
								match.error = true;
							}
						}
					}
				}
			},
			removeErrors() {
				for (const box of this.gridState) {
					for (const cell of box) {
						cell.error = false;
					}
				}
			},
			focusActiveCellInput() {
				const activeCellContainer = document.getElementsByClassName('grid-cell active');

				if (activeCellContainer && activeCellContainer.length) {
					const input = activeCellContainer[0].getElementsByTagName('input')[0];

					input.focus();
				}
			},
			toggleLock(isLocked) {
				for (const cell of this.cellsWithValue) {
					cell.locked = isLocked;
				}
			},
			quickNotes() {
				for (const box of this.gridState) {
					for (const cell of box) {
						if (!cell.val) {
							const cellsInRange = this.getCellsInRangeOf(cell);
							const adjacentNumbers = [];

							for (const adjacent of cellsInRange) {
								adjacentNumbers.push(adjacent.val);
							}

							for (let i = 1; i < 10; i++) {
								if (!adjacentNumbers.includes(i) && !cell.notes.includes(i)) {
									cell.notes.push(i);
								}
							}
						}
					}
				}
			},
			/**
			 * Prevents an event
			 */
			preventEvent(e) {
				e.preventDefault();
				e.stopPropagation();
				return false;
			}
		}
	};
</script>

<template>
	<div v-if="gridState.length" id="game-grid-container">
		<div v-for="box in gridState" class="grid-box">
			<div
				v-for="cell in box"
				class="grid-cell"
				:class="getCellClass(cell)"
			>
				<div class="cell-notes">
					<span
						v-for="i of 9"
						class="cell-note"
						:class="getNotesClass(cell, i)"
					>
						{{ i }}
					</span>
				</div>
				<input
					type="text"
					maxlength="1"
					:value="cell.val"
					@focus="handleCellFocus(cell)"
					@keydown.prevent="handleCellInput($event)"
					@paste.prevent="preventEvent"
					@dragstart.prevent="preventEvent"
					@dragend.prevent="preventEvent"
					@drop.prevent="preventEvent"
				/>
			</div>
		</div>
	</div>
</template>

<style scoped lang="scss">
	#game-grid-container {
		aspect-ratio: 1/1;
		background-color: #333;
		border-bottom: 4px solid #fff;
		box-shadow: 0 0 40px rgba(0, 0, 0, .8);
		display: flex;
		flex-wrap: wrap;

		.grid-box {
			aspect-ratio: 1/1;
			border-left: 4px solid #fff;
			border-top: 4px solid #fff;
			display: flex;
			flex-basis: 33.33%;
			flex-wrap: wrap;

			&:nth-of-type(3n) {
				border-right: 4px solid #fff;
			}

			.grid-cell {
				aspect-ratio: 1/1;
				border-bottom: 1px solid #fff;
				border-right: 1px solid #fff;
				flex-basis: 33.33%;
				position: relative;

				&:nth-of-type(3n) {
					border-right: none;
				}

				&:nth-child(7),
				&:nth-child(8),
				&:nth-child(9) {
					border-bottom: none;
				}

				&.in-range {
					background-color: rgba(255, 255, 255, .2);
				}

				&.matched-in-range {
					background-color: rgba(200, 150, 70, .3);
				}

				&.matched {
					background-color: rgba(0, 180, 100, .3);

					input {
						text-shadow: 0 0 0 rgba(0, 255, 100, 1);
					}
				}

				&.error {
					background-color: rgba(255, 50, 50, .3);

					input {
						text-shadow: 0 0 0 rgba(220, 130, 30, .9);
					}
				}

				&.locked {
					input {
						text-shadow: 0 0 0 rgba(255, 255, 255, 1);
					}

					&.error {
						input {
							text-shadow: 0 0 0 rgba(255, 150, 150, 1);
						}
					}
				}

				&.active,
				&:hover {
					background-color: rgba(0, 191, 255, .3);
				}

				.cell-notes {
					aspect-ratio: 1/1;
					display: flex;
					flex-wrap: wrap;
					pointer-events: none;

					.cell-note {
						align-items: center;
						aspect-ratio: 1/1;
						color: #fff;
						display: flex;
						flex-basis: 33.33%;
						font-size: 13px;
						justify-content: center;
						user-select: none;
						-webkit-touch-callout: none;
						-webkit-user-select: none;
						visibility: hidden;

						&.active {
							visibility: visible;
						}

						&.matched {
							background-color: rgba(0, 215, 0, .5);
						}
					}
				}

				input {
					background-color: transparent;
					border: none;
					bottom: 0;
					box-shadow: none !important;
					color: transparent;
					cursor: default;
					display: block;
					font-size: 36px;
					font-weight: 700;
					height: 100%;
					left: 0;
					line-height: 3 !important;
					padding: 0;
					position: absolute;
					right: 0;
					text-align: center;
					text-shadow: 0 0 0 rgba(220, 130, 30, 1);
					top: 0;
					width: 100%;
					-webkit-appearance: none;

					&:active,
					&:focus,
					&:target {
						outline: none;
					}
				}
			}
		}
	}
</style>
