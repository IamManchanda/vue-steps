<template>
  <div>
    <div class="grid-x grid-margin-x grid-padding-y">
      <div class="medium-8 medium-offset-2  large-6 large-offset-3 cell">
        <horizontal-stepper 
          :steps="demoSteps" 
          @completed-step="completeStep"
          @active-step="isStepActive" 
          @stepper-finished="alert"
        />
      </div>
    </div>
  </div>
</template>

<script>
import HorizontalStepper from "./step-container/HorizontalStepper.vue";

import StepOne from "./steps/StepOne.vue";
import StepTwo from "./steps/StepTwo.vue";
import StepThree from "./steps/StepThree.vue";

export default {
  name: "FormStepper",
  components: {
    "horizontal-stepper": HorizontalStepper
  },
  data() {
    return {
      demoSteps: [
        {
          icon: "mail",
          name: "first",
          title: "Sample 1",
          component: StepOne,
          completed: false
        },
        {
          icon: "report_problem",
          name: "second",
          title: "Sample 2",
          component: StepTwo,
          completed: false
        },
        {
          icon: "announcement",
          name: "third",
          title: "Sample 3",
          component: StepThree,
          completed: false
        }
      ],
      activeStep: 0
    };
  },
  computed: {},
  methods: {
    completeStep(payload) {
      this.demoSteps.forEach(step => {
        if (step.name === payload.name) {
          step.completed = true;
        }
      });
    },
    isStepActive(payload) {
      this.demoSteps.forEach(step => {
        if (step.name === payload.name) {
          if (step.completed === true) {
            step.completed = false;
          }
        }
      });
    },
    alert(payload) {
      alert("end");
    }
  }
};
</script>

<style lang="scss">
</style>
