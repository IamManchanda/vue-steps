<template>
  <div class="stepper-box">
    <div class="top">
      <div class="steps-wrapper">
        <template v-for="(step, index) in steps">
          <div :class="['step', isStepActive(index, step)]" :key="index" :style="{width: `${100 / steps.length}%`}">
            <div class="step-title">
              <h4>{{step.title}}</h4>
            </div>
          </div>
        </template>
      </div>
    </div>
    <div class="content">
        <transition :enter-active-class="enterAnimation" :leave-active-class="leaveAnimation" mode="out-in">
          <!--If keep alive-->
          <keep-alive v-if="keepAlive">
              <component :is="steps[currentStep.index].component" :clickedNext="nextButton[currentStep.name]" @can-continue="proceed" @change-next="changeNextBtnValue" :current-step="currentStep"></component>
          </keep-alive>
          <!--If not show component and destroy it in each step change-->
          <component v-else :is="steps[currentStep.index].component" :clickedNext="nextButton[currentStep.name]" @can-continue="proceed" @change-next="changeNextBtnValue" :current-step="currentStep"></component>
        </transition>
    </div>
    <div :class="['bottom', (currentStep.index > 0) ? '' : 'only-next']">
        <button v-if="currentStep.index > 0" class="button secondary" @click="backStep()">
            <span>Back</span>
        </button>
        <button :class="['button primary', !canContinue ? 'disabled' : '']" @click="nextStep()">
            <span v-if="finalStep">Finish</span>
            <span v-else>Next</span>
        </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    topButtons: {
      type: Boolean,
      default: false
    },
    steps: {
      type: Array,
      default: function() {
        return [
          {
            icon: "mail",
            name: "first",
            title: "Sample title 1",
            subtitle: "Subtitle sample"
          },
          {
            icon: "report_problem",
            name: "second",
            title: "Sample title 2",
            subtitle: "Subtitle sample"
          }
        ];
      }
    },
    keepAlive: {
      type: Boolean,
      default: true
    }
  },

  data() {
    return {
      currentStep: {},
      previousStep: {},
      nextButton: {},
      canContinue: false,
      finalStep: false
    };
  },

  computed: {
    enterAnimation() {
      if (this.currentStep.index < this.previousStep.index) {
        return "animated quick fadeInLeft";
      } else {
        return "animated quick fadeInRight";
      }
    },
    leaveAnimation() {
      if (this.currentStep.index > this.previousStep.index) {
        return "animated quick fadeOutLeft";
      } else {
        return "animated quick fadeOutRight";
      }
    }
  },

  methods: {
    isStepActive(index, step) {
      if (this.currentStep.index === index) {
        return "activated";
      } else {
        return "deactivated";
      }
    },

    activateStep(index, back = false) {
      if (this.steps[index]) {
        this.previousStep = this.currentStep;
        this.currentStep = {
          name: this.steps[index].name,
          index: index
        };

        if (index + 1 === this.steps.length) {
          this.finalStep = true;
        } else {
          this.finalStep = false;
        }

        if (!back) {
          this.$emit("completed-step", this.previousStep);
        }
      }
      this.$emit("active-step", this.currentStep);
    },

    nextStepAction() {
      this.nextButton[this.currentStep.name] = true;
      if (this.canContinue) {
        if (this.finalStep) {
          this.$emit("stepper-finished", this.currentStep);
        }
        let currentIndex = this.currentStep.index + 1;

        this.activateStep(currentIndex);
      }
      this.canContinue = false;
      this.$forceUpdate();
    },

    nextStep() {
      if (!this.$listeners || !this.$listeners["before-next-step"]) {
        this.nextStepAction();
      }

      this.canContinue = false;

      this.$emit(
        "before-next-step",
        { currentStep: this.currentStep },
        (next = true) => {
          this.canContinue = true;
          if (next) {
            this.nextStepAction();
          }
        }
      );
    },
    backStep() {
      this.$emit("clicking-back");
      let currentIndex = this.currentStep.index - 1;
      if (currentIndex >= 0) {
        this.activateStep(currentIndex, true);
      }
    },

    proceed(payload) {
      this.canContinue = payload.value;
    },

    changeNextBtnValue(payload) {
      this.nextButton[this.currentStep.name] = payload.nextBtnValue;
      this.$forceUpdate();
    }
  },

  created() {
    // Initiate stepper
    this.activateStep(0);
    this.steps.forEach(step => {
      this.nextButton[step.name] = false;
    });
  }
};
</script>

<style src="./HorizontalStepper.scss" scoped lang="scss">
</style>
