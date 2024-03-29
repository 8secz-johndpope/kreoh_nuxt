<script>
import { mapState, mapMutations } from 'vuex'
export default {
  props: {
    options: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
      editing: false,
      editing_interest_id: null
    }
  },
  computed: {
    ...mapState('creator', ['site_props']),
    interests() {
      return this.site_props.resume_page_inputs.interests_section
    },
    title: {
      set(value) {
        this.setResumeSection({
          section_type: 'interests',
          id: this.editing_interest_id,
          prop: 'title',
          value
        })
      },
      get() {
        return this.interests[this.editing_interest_id].title
      }
    },
    details_available: {
      set(value) {
        this.setResumeSection({
          section_type: 'interests',
          id: this.editing_interest_id,
          prop: 'details',
          type: 'available',
          value
        })
      },
      get() {
        return this.interests[this.editing_interest_id].details.available
      }
    },
    details: {
      set(value) {
        this.setResumeSection({
          section_type: 'interests',
          id: this.editing_interest_id,
          prop: 'details',
          type: 'value',
          value
        })
      },
      get() {
        return this.interests[this.editing_interest_id].details.value
      }
    }
  },
  methods: {
    ...mapMutations({
      setResumeSection: 'creator/setResumeSection',
      addInterest: 'creator/addInterest',
      deleteInterest: 'creator/deleteInterest'
    }),
    selectInterest(id) {
      if (this.options.editing) {
        if (document.querySelector('.interest-selected')) {
          if (
            this.$refs['interest_' + id][0] ===
            document.querySelector('.interest-selected')
          ) {
            this.$refs['interest_' + id][0].classList.remove(
              'interest-selected'
            )
            this.editing = false
            this.editing_interest_id = null
          } else {
            document
              .querySelector('.interest-selected')
              .classList.remove('interest-selected')
            this.$refs['interest_' + id][0].classList.add('interest-selected')
            this.editing = true
            this.editing_interest_id = id
          }
        } else {
          this.$refs['interest_' + id][0].classList.add('interest-selected')
          this.editing = true
          this.editing_interest_id = id
        }
      }
    },
    deleteInt() {
      const editingInterestId = this.editing_interest_id
      this.editing_interest_id = null
      this.deleteInterest({
        id: editingInterestId
      })
      this.editing = false
    }
  }
}
</script>

<template>
  <v-layout
    class="d-flex flex-column align-center"
    :class="{
      'interests-main-container': !options.editing,
      'interests-container': options.editing
    }"
  >
    <div
      class="d-flex flex-column align-center mb-3 pa-6 elevation-2"
      :class="{
        'interests-viewer': options.editing,
        'interests-main-viewer': !options.editing
      }"
    >
      <div class="interests-wrapper d-flex flex-column align-center">
        <span v-if="!options.editing" class="title font-weight-bold">
          Hobbies/Interests:
        </span>
        <div
          v-for="interest in interests"
          :key="interest.id"
          :ref="'interest_' + interest.id"
          class="d-flex flex-column align-center elevation-1 ma-2 pa-6"
          :class="{
            'selectable interest': options.editing,
            'main-interest': !options.editing
          }"
          @click="selectInterest(interest.id)"
        >
          <span class="title mb-2">{{ interest.title }}</span>
          <span
            v-if="interest.details.available"
            class="interest-details caption desc"
          >
            {{ interest.details.value }}
          </span>
        </div>
      </div>
    </div>
    <div
      v-if="options.editing"
      class="section-editor d-flex flex-column align-center elevation-2 pa-2"
    >
      <transition
        enter-active-class="animated fadeIn faster"
        leave-active-class="animated fadeOut faster"
        mode="out-in"
      >
        <div
          v-if="!editing"
          key="placeholder"
          class="editor-placeholder d-flex flex-column align-center justify-center"
        >
          <span class="headline">Select an item above to begin editing!</span>
          <v-btn color="info" class="mt-4" rounded large @click="addInterest()">
            <span>Add a New Interest</span>
          </v-btn>
        </div>
        <div v-else key="editor" class="editor d-flex flex-column align-center">
          <div class="d-flex flex-column align-center">
            <v-text-field
              v-model="title"
              :disabled="!editing"
              outlined
              dense
              label="Interest Title"
            ></v-text-field>
            <div class="d-flex flex-column align-center">
              <v-switch
                v-model="details_available"
                inset
                dense
                label="Enable Description"
              ></v-switch>
              <v-textarea
                v-model="details"
                outlined
                dense
                label="Describe Your Interest"
              ></v-textarea>
            </div>
            <v-btn
              color="error"
              class="align-self-center mb-3"
              width="10"
              @click="deleteInt()"
            >
              <v-icon>mdi-delete-forever</v-icon>
            </v-btn>
          </div>
        </div>
      </transition>
    </div>
  </v-layout>
</template>

<style lang="scss" scoped>
.interests-container {
  width: 100%;
  height: 100%;
}

.interests-main-container {
  width: 100%;
}

.interests-viewer {
  height: 50%;
  width: 100%;
  overflow: auto;
  position: relative;
}

.interests-main-viewer {
  height: 100%;
  width: 100%;
  overflow: auto;
  position: relative;
}

.interests-wrapper {
  width: 100%;
}

.interest {
  width: 40%;
  border-radius: 10px;
}

.main-interest {
  width: 90%;
  border-radius: 10px;
}

.interest-selected {
  border: 1px solid #0066ff;
}

.selectable:hover {
  border: 1px solid #0066ff;
  cursor: pointer;
}

.interest-details {
  text-align: center;
}

.section-editor {
  position: relative;
  height: 50%;
  width: 100%;
  // border: 1px solid;
  overflow: auto;
}

.editor-placeholder {
  height: 100%;
  width: 100%;
  border: 1px dashed #777;
  border-radius: 10px;
}

.desc {
  white-space: pre-wrap;
  text-align: center;
  word-wrap: break-word;
}
</style>
