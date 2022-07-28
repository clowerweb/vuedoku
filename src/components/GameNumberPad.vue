<script>
	export default {
		props: {
			cellsWithValue: {
				type: Array,
				required: true
			}
		},
		computed: {
			valuesUsed() {
				const result = [];

				for (const cell of this.cellsWithValue) {
					result.push(cell.val);
				}

				return result;
			}
		},
		methods: {
			getRemaining(i) {
				return 9 - this.valuesUsed.filter(x => x === i).length;
			}
		}
	};
</script>

<template>
	<ul>
		<li v-for="i in 9">
			<button
				:class="getRemaining(i) === 0 ? 'secondary' : 'primary'"
				@click="$emit('set-number', i)"
			>
				{{ i }}
				<span>
					{{ getRemaining(i) }}
				</span>
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
					border-radius: 4px 0 0 4px;
				}
			}

			&:last-of-type {
				button {
					border-radius: 0 4px 4px 0;
				}
			}

			+ li {
				border-left: 1px solid #fff;
			}

			button {
				border: 1px solid transparent;
				color: #fff;
				font-size: 26px;
				padding: 6px 24.67px 22px;
				position: relative;
				user-select: none;
				-webkit-touch-callout: none;
				-webkit-user-select: none;

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

				&.secondary {
					background-color: #6c757d;
					border-color: #6c757d;
					cursor: default;
					pointer-events: none;

					&:active,
					&:focus,
					&:hover,
					&:target {
						background-color: #5c636a;
						border-color: #565e64;
					}
				}

				> span {
					bottom: 0;
					color: #fff;
					font-size: 12px;
					left: 0;
					padding: 0 0 5px;
					position: absolute;
					right: 0;
					text-align: center;
					user-select: none;
					-webkit-touch-callout: none;
					-webkit-user-select: none;
					width: 100%;
				}
			}
		}
	}
</style>
