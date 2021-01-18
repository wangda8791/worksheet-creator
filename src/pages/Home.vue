<template>
  <b-container>
    <b-modal
      id="modal-match"
      :title="match.id ? 'Update match' : 'Create match'"
      @ok="onCreateOrUpdate"
    >
      <form ref="form">
        <div class="row">
          <div class="col col-sm-12">
            <b-form-group label="Word" label-for="word-input">
              <b-form-input
                id="word-input"
                v-model="match.word"
                :state="validateState('word')"
                aria-describedby="input-word-feedback"
                autofocus
                required
              ></b-form-input>
              <b-form-invalid-feedback id="input-word-feedback"
                >Word is a required field.</b-form-invalid-feedback
              >
            </b-form-group>
          </div>
          <div class="col col-sm-12">
            <b-form-group label="Definition" label-for="definition-input">
              <b-form-textarea
                id="definition-input"
                v-model="match.definition"
                :state="validateState('definition')"
                :rows="10"
                aria-describedby="input-definition-feedback"
                required
              ></b-form-textarea>
              <b-form-invalid-feedback id="input-definition-feedback"
                >Definition is a required field.</b-form-invalid-feedback
              >
            </b-form-group>
          </div>
        </div>
      </form>
    </b-modal>
    <div class="row mb-1">
      <div class="col col-sm-12">
        <b-button
          id="btn-new"
          type="button"
          size="md"
          variant="secondary"
          class="float-right"
          @click="onNewMatch"
        >
          <i class="fa fa-plus prepend" />
          Create match
        </b-button>
        <b-button
          id="btn-preview"
          type="button"
          size="md"
          variant="secondary"
          class="mr-1 float-right"
          @click="onPreview"
        >
          <i class="fa fa-plus prepend" />
          Preview
        </b-button>
      </div>
    </div>
    <div class="row">
      <div class="col col-sm-12">
        <b-table
          :items="matches"
          :fields="fields"
          outlined
          show-empty
          thead-class="thead"
        >
          <template #cell(index)="data">
            {{ data.index + 1 }}
          </template>
          <template #cell(action)="data">
            <div class="row flex-row-2">
              <b-button
                type="button"
                size="sm"
                variant="secondary"
                @click="deleteMatch(data.item)"
                class="mr-1"
                >Delete</b-button
              >
              <b-button
                type="button"
                size="sm"
                variant="secondary"
                @click="editMatch(data.item)"
                >Edit</b-button
              >
            </div>
          </template>
        </b-table>
      </div>
    </div>
  </b-container>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],
  data() {
    return {
      match: {
        word: "",
        definition: "",
      },
      fields: [
        {
          key: "index",
          label: "NO.",
          sortable: true,
        },
        {
          key: "word",
          label: "WORD",
          sortable: true,
        },
        {
          key: "definition",
          label: "DEFINITION",
          sortable: true,
        },
        {
          key: "action",
          label: "",
          sortable: false,
        },
      ],
      matches: [],
    };
  },
  validations: {
    match: {
      word: {
        required,
      },
      definition: {
        required,
      },
    },
  },
  methods: {
    validateState(name) {
      const { $dirty, $error } = this.$v.match[name];
      return $dirty ? !$error : null;
    },
    checkFormValidity() {
      this.$v.match.$touch();
      if (this.$v.match.$anyError) {
        return false;
      }
      return true;
    },
    onNewMatch() {
      this.match = {
        word: "",
        definition: "",
      };
      this.$v.$reset();
      this.$bvModal.show("modal-match");
    },
    onPreview() {},
    onCreateOrUpdate(bvModalEvt) {
      bvModalEvt.preventDefault();
      if (this.match.id) {
        this.doUpdateMatch();
      } else {
        this.doCreateMatch();
      }
    },
    async doCreateMatch() {
      if (!this.checkFormValidity()) {
        return;
      }
      this.match.id = Date.now();
      this.matches.push(this.match);
      this.$bvModal.hide("modal-match");
    },
    async doUpdateMatch() {
      if (!this.checkFormValidity()) {
        return;
      }
      this.matches = this.matches.map((item) => {
        if (item.id === this.match.id) {
          return { ...this.match };
        }
        return item;
      });
      this.$bvModal.hide("modal-match");
    },
    deleteMatch(item) {
      this.$bvModal
        .msgBoxConfirm("Are you sure to delete this match?", {
          title: "Please Confirm",
          okTitle: "YES",
          cancelTitle: "NO",
          footerClass: "p-2",
          hideHeaderClose: false,
          centered: true,
        })
        .then((value) => {
          if (value) {
            this.doDeleteMatch(item.id);
          }
        })
        .catch((e) => {
          console.log(e);
        });
    },
    doDeleteMatch(id) {
      this.matches = this.matches.filter((item) => item.id !== id);
    },
    editMatch(item) {
      this.match = { ...item };
      this.$v.$reset();
      this.$bvModal.show("modal-match");
    },
  },
};
</script>