<script>
	export default {
		props: {
			cellsWithValue: {
				type: Array,
				required: true
			}
		},
		methods: {
			getRemaining(i) {
				return 9 - this.cellsWithValue.filter(x => x.val === i).length;
			},
		},
	};
</script>

<template>
	<ul class="btn-group">
		<li v-for="i in 9">
			<button
				class="btn"
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
			button {
				font-size: 26px;
				padding: 6px 24.67px 22px;
				position: relative;

				> span {
					bottom: 0;
					color: #fff;
					font-size: 12px;
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
