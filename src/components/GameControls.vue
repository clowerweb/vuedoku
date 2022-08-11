<script>
	export default {
		props: {
			locked: {
				type: Boolean,
				required: true,
				default: false
			},
			noteMode: {
				type: Boolean,
				required: true,
				default: false
			}
		}
	};
</script>

<template>
	<ul>
		<li>
			<button
				:class="locked ? 'success' : 'primary'"
				@click="$emit('toggle-lock')"
			>
				<i class="fa-solid" :class="locked ? 'fa-lock' : 'fa-lock-open'"></i>
				<span>{{ locked ? 'Unlock Puzzle' : 'Lock Puzzle' }}</span>
			</button>
		</li>
		<li>
			<button
				class="primary"
				:class="noteMode ? 'success' : 'primary'"
				@click="$emit('toggle-notes')"
			>
				<i class="fa-solid fa-pencil"></i>
				<span>{{ noteMode ? 'Notes On' : 'Notes Off' }}</span>
			</button>
		</li>
		<li>
			<button
				class="primary"
				@click="$emit('quick-notes')"
			>
				<i class="fa-solid fa-feather-pointed"></i>
				<span>Quick Notes</span>
			</button>
		</li>
		<li>
			<button class="primary" @click="$emit('erase')">
				<i class="fa-solid fa-eraser"></i>
				<span>Erase</span>
			</button>
		</li>
		<li>
			<button
				class="secondary"
				@click.prevent="$emit('undo')"
			>
				<i class="fa-solid fa-rotate-left"></i>
				<span>Undo</span>
			</button>
		</li>
		<li>
			<button class="secondary" @click.prevent="$emit('redo')">
				<i class="fa-solid fa-rotate-right"></i>
				<span>Redo</span>
			</button>
		</li>
		<li>
			<button
				class="secondary"
				@click.prevent="$emit('hint')"
			>
				<i class="fa-solid fa-lightbulb"></i>
				<span>Hint</span>
			</button>
		</li>
		<li>
			<button class="secondary" @click="$emit('solve')">
				<i class="fa-solid fa-wand-sparkles"></i>
				<span>Solve</span>
			</button>
		</li>
		<li>
			<button
				class="danger"
				@click="$emit('clear-all')"
			>
				<i class="fa-solid fa-trash-can"></i>
				<span>Clear All</span>
			</button>
		</li>
	</ul>
</template>

<style scoped lang="scss">
	ul {
		display: flex;
		justify-content: center;
		margin-top: 5px;

		> li {
			&:first-of-type {
				button {
					border-bottom-left-radius: 4px;
					border-top-left-radius: 4px;
				}
			}

			&:last-of-type {
				button {
					border-bottom-right-radius: 4px;
					border-top-right-radius: 4px;
				}
			}

			+ li {
				border-left: 1px solid #fff;
			}

			button {
				border: 1px solid transparent;
				color: #fff;
				font-size: 18px;
				padding: 12px 23px 22px;
				position: relative;

				&:nth-of-type(1) {
					width: 65.78px;
				}

				&.primary {
					background-color: #0d6efd;
					border-color: #0d6efd;

					&:active,
					&:focus,
					&:hover,
					&:target {
						background-color: #0b5ed7;
						border-color: #0a58ca;
					}
				}

				&.success {
					background-color: #198754;
					border-color: #198754;

					&:active,
					&:focus,
					&:hover,
					&:target {
						background-color: #157347;
						border-color: #146c43;
					}
				}

				&.secondary {
					background-color: #6c757d;
					border-color: #6c757d;

					&:active,
					&:focus,
					&:hover,
					&:target {
						background-color: #5c636a;
						border-color: #565e64;
					}
				}

				&.danger {
					background-color: #dc3545;
					border-color: #dc3545;

					&:active,
					&:focus,
					&:hover,
					&:target {
						background-color: #bb2d3b;
						border-color: #b02a37;
					}
				}

				> i {
					max-width: 17px;
					width: 17px;
				}

				> span {
					bottom: 0;
					color: #fff;
					font-size: 10px;
					left: 0;
					padding: 0 0 5px;
					position: absolute;
					right: 0;
					text-align: center;
					width: 100%;
				}
			}
		}
	}
</style>
