<script>
  import Modal from '@/components/Modal.vue';

  export default {
    components: {Modal},
    props: {
      locked: {
        type: Boolean,
        required: true,
        default: false,
      },
      noteMode: {
        type: Boolean,
        required: true,
        default: false,
      },
      openModals: {
        type: Array,
        required: true,
      },
    },
  };
</script>

<template>
  <div>
    <ul class="main-controls btn-group">
      <li>
        <button
          class="btn"
          :class="locked ? 'success' : 'primary'"
          @click="$emit('toggle-lock')"
        >
          <i
            class="fa-solid"
            :class="locked ? 'fa-lock' : 'fa-lock-open'"
            aria-hidden="true"
          ></i>
          <span>{{ locked ? 'Unlock' : 'Lock' }}</span>
        </button>
      </li>
      <li>
        <button
          class="btn primary"
          :class="noteMode ? 'success' : 'primary'"
          @click="$emit('toggle-notes')"
        >
          <i class="fa-solid fa-pencil" aria-hidden="true"></i>
          <span>{{ noteMode ? 'Notes On' : 'Notes Off' }}</span>
        </button>
      </li>
      <li>
        <button class="btn primary" @click="$emit('quick-notes')">
          <i class="fa-solid fa-feather-pointed" aria-hidden="true"></i>
          <span>Quick Notes</span>
        </button>
      </li>
      <li>
        <button class="btn primary" @click="$emit('erase')">
          <i class="fa-solid fa-eraser" aria-hidden="true"></i>
          <span>Erase</span>
        </button>
      </li>
      <li>
        <button
          class="btn secondary"
          @click.prevent="$emit('undo')"
        >
          <i class="fa-solid fa-rotate-left" aria-hidden="true"></i>
          <span>Undo</span>
        </button>
      </li>
      <li>
        <button class="btn secondary" @click.prevent="$emit('redo')">
          <i class="fa-solid fa-rotate-right" aria-hidden="true"></i>
          <span>Redo</span>
        </button>
      </li>
      <li>
        <button class="btn secondary" @click.prevent="$emit('hint')">
          <i class="fa-solid fa-lightbulb" aria-hidden="true"></i>
          <span>Hint</span>
        </button>
      </li>
      <li>
        <button class="btn secondary" @click="$emit('solve')">
          <i class="fa-solid fa-wand-sparkles" aria-hidden="true"></i>
          <span>Solve</span>
        </button>
      </li>
      <li>
        <button class="btn danger" @click="$emit('clear')">
          <i class="fa-solid fa-trash-can" aria-hidden="true"></i>
          <span>Clear...</span>
        </button>
      </li>
    </ul>

    <Modal
      :set="theId = 'clear-modal'"
      :modal-id="theId"
      :is-open="openModals.includes(theId)"
      cancel-text="Cancel"
      @closeModal="$emit('close-modal', theId)"
    >
      <template v-slot:title>Clear Board Elements</template>
      <template v-slot:description>
        <ul class="modal-options">
          <li>
            <button class="btn primary" @click="$emit('clear-notes')">Clear Notes</button>
          </li>
          <li>
            <button class="btn primary" @click="$emit('clear-unlocked')">Clear Unlocked</button>
          </li>
          <li>
            <button class="btn primary" @click="$emit('clear-notes-unlocked')">
              Clear Notes & Unlocked
            </button>
          </li>
          <li>
            <button class="btn danger" @click="$emit('clear-all')">Clear EVERYTHING</button>
          </li>
        </ul>
      </template>
    </Modal>
  </div>
</template>

<style scoped lang="scss">
  ul.main-controls {
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

  .modal-options {
    li {
      + li {
        margin-top: 15px;
      }

      button {
        display: block;
        width: 100%;
      }
    }
  }
</style>
