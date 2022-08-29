<script>
  import Modal from '@/components/Modal.vue';

  export default {
    components: { Modal },
    props: {
      openModals: {
        type: Array,
        required: true,
      },
    },
  };
</script>

<template>
  <div class="wrapper flex">
    <button @click="$emit('open')">
      <i class="fa-solid fa-cog" aria-hidden="true"></i>
      <span class="sr">Settings</span>
    </button>

    <Modal
      :set="theId = 'settings-modal'"
      :modal-id="theId"
      :is-open="openModals.includes(theId)"
      accept-text="Save"
      @closeModal="$emit('close-modal', theId)"
    >
      <template v-slot:title>Settings</template>
      <template v-slot:description>
        <div class="row">
          <span class="label">New:</span>
          <ul class="btn-group">
            <li>
              <button class="btn primary" @click="$emit('new-game', 'easy')">
                Easy
              </button>
            </li>
            <li>
              <button class="btn primary" @click="$emit('new-game', 'medium')">
                Medium
              </button>
            </li>
            <li>
              <button class="btn primary" @click="$emit('new-game', 'hard')">
                Hard
              </button>
            </li>
            <li>
              <button class="btn primary" @click="$emit('new-game', 'expert')">
                Expert
              </button>
            </li>
          </ul>
        </div>
      </template>
    </Modal>
  </div>
</template>

<style scoped lang="scss">
  .wrapper {
    padding: 15px 0;

    > button {
      color: #fff;
      font-size: 24px;
      margin-left: auto;
    }

    .row {
      align-items: center;
      display: flex;

      > span {
        flex-basis: 20%;
        font-size: 20px;
        font-weight: 700;
      }
    }

    .btn-group {
      li {
        &:first-of-type {
          button {
            border-radius: 4px 0 0 4px !important;
          }
        }

        &:last-of-type {
          button {
            border-radius: 0 4px 4px 0 !important;
          }
        }
      }
    }
  }
</style>
