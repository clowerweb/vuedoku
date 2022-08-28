<script>
	export default {
		props: {
			modalId: {
				type: String,
				required: true,
			},
			isOpen: {
				type: Boolean,
				required: true,
			},
			acceptText: {
				type: String,
				required: false,
				default: null,
			},
			cancelText: {
				type: String,
				required: false,
				default: 'Close',
			},
		},
		methods: {
			closeModal() {
				this.$emit('close-modal', this.modalId);
			},
		},
	};
</script>

<template>
	<div
		:id="modalId"
		class="modal"
		:class="isOpen ? 'active' : ''"
		tabindex="-1"
		:aria-labelledby="`${modalId}-title`"
		aria-hidden="true"
	>
		<div class="modal-content">
			<header>
				<h4 :id="`${modalId}-title`">
					<slot name="title"></slot>
				</h4>
				<button
					type="button"
					class="close"
					aria-label="Close"
					@click="closeModal"
				></button>
			</header>

			<main>
				<slot name="description"></slot>
			</main>

			<footer class="clear">
				<div class="pull-right">
					<button type="button" class="btn secondary" @click="closeModal">{{ cancelText }}</button>
					<button
						v-if="acceptText"
						type="button"
						class="btn primary"
					>
						{{ acceptText }}
					</button>
				</div>
			</footer>
		</div>
	</div>
</template>

<style scoped lang="scss">
	.modal {
		background-color: #fff;
		border-radius: 8px;
		box-shadow: 0 0 40px rgba(0, 0, 0, .8);
		max-width: 600px;
		opacity: 0;
		padding: 30px 15px;
		position: fixed;
		top: calc(-100%);
		transition: top .4s ease-in-out, opacity .6s ease-in-out;
		width: 100%;

		&.active {
			display: block;
			opacity: 1;
			top: 60px;
		}

		header {
			margin-bottom: 30px;
			position: relative;

			h4 {
				font-size: 20px;
				margin: 0;
				padding: 0;
			}

			button {
				background: transparent url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23000'%3e%3cpath d='M.293.293a1 1 0 0 1 1.414 0L8 6.586 14.293.293a1 1 0 1 1 1.414 1.414L9.414 8l6.293 6.293a1 1 0 0 1-1.414 1.414L8 9.414l-6.293 6.293a1 1 0 0 1-1.414-1.414L6.586 8 .293 1.707a1 1 0 0 1 0-1.414z'/%3e%3c/svg%3e") center/1em auto no-repeat;
				box-sizing: content-box;
				color: #000;
				height: 1em;
				opacity: .5;
				padding: 8px;
				position: absolute;
				top: -50%;
				right: 0;
				width: 1em;
				-webkit-appearance: button;

				&:hover {
					opacity: .75;
					text-decoration: none;
				}
			}
		}

		main {
			margin-bottom: 30px;
		}
	}
</style>
