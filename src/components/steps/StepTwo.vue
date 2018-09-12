<template>
  <div class="step-container">
    <h2>Step 2</h2>
    <div class="field-container">
      <label :class="($v.form.username.$error) ? 'is-invalid-input' : ''">
        Username
        <input 
          :class="($v.form.username.$error) ? 'is-invalid-input' : ''"
          type="text" 
          placeholder="Text input"
          v-model="form.username"
        />
      </label>
      <p v-if="$v.form.username.$error" class="form-error is-visible">This username is invalid</p>
    </div>
    <div class="field-container">
      <label :class="($v.form.username.$error) ? 'is-invalid-input' : ''">
        Email
        <input 
          :class="($v.form.username.$error) ? 'is-invalid-input' : ''"  
          type="text" 
          placeholder="Email input" 
          v-model="form.demoEmail"
        />
      </label>
      <p v-if="$v.form.demoEmail.$error" class="form-error is-visible">This email is invalid</p>
    </div>
    <div class="field-container">
      <label :class="($v.form.username.$error) ? 'is-invalid-input' : ''">
        Message
        <textarea 
          :class="($v.form.username.$error) ? 'is-invalid-input' : ''"  
          placeholder="Textarea" 
          v-model="form.message">
        </textarea>
      </label>
      <p v-if="$v.form.demoEmail.$error" class="form-error is-visible">This message is required</p>
    </div>
  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, email } from "vuelidate/lib/validators";

export default {
  props: ["clickedNext", "currentStep"],
  mixins: [validationMixin],
  data() {
    return {
      form: {
        username: "",
        demoEmail: "",
        message: ""
      }
    };
  },
  validations: {
    form: {
      username: {
        required
      },
      demoEmail: {
        required,
        email
      },
      message: {
        required
      }
    }
  },
  watch: {
    $v: {
      handler: function(val) {
        if (!val.$invalid) {
          this.$emit("can-continue", { value: true });
        } else {
          this.$emit("can-continue", { value: false });
          setTimeout(() => {
            this.$emit("change-next", { nextBtnValue: false });
          }, 3000);
        }
      },
      deep: true
    },

    clickedNext(val) {
      console.log(val);
      if (val === true) {
        this.$v.form.$touch();
      }
    }
  },
  mounted() {
    if (!this.$v.$invalid) {
      this.$emit("can-continue", { value: true });
    } else {
      this.$emit("can-continue", { value: false });
    }
  }
};
</script>
<style lang="scss" scoped>
.step-container {
  padding: 1rem;
}
</style>
