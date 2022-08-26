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
	<ul class="btn-group">
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
				@click="$emit('clear')"
			>
				<i class="fa-solid fa-trash-can"></i>
				<span>Clear...</span>
			</button>
		</li>
	</ul>
</template>

<style scoped lang="scss">
	ul {
		> li {
			button {
				padding-bottom: 22px;

				&:nth-of-type(1) {
					width: 65.78px;
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
