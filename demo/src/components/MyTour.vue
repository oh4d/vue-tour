<template>
  <div>
    <button @click="$tours['myTour'].start()" class="btn btn-lg mr-2">Start the tour</button>
    <button @click="nextStep" class="btn btn-lg mr-2">Next step</button>
    <button @click="showLastStep" class="btn btn-lg">Show last step</button>

    <v-tour name="myTour" :steps="steps" :callbacks="callbacks">
      <template slot-scope="tour">
        <transition name="fade">
          <v-step
            v-if="tour.currentStep === index"
            v-for="(step, index) of tour.steps"
            :key="index"
            :step="step"
            :previous-step="tour.previousStep"
            :next-step="tour.nextStep"
            :stop="tour.stop"
            :is-first="tour.isFirst"
            :is-last="tour.isLast"
            :labels="tour.labels"
          >
            <template v-if="tour.currentStep === 2">
              <div slot="actions">
                <button @click="tour.previousStep" class="btn btn-primary mr-2">Previous step</button>
                <button @click="tour.nextStep" class="btn btn-primary">Next step</button>
              </div>
            </template>
          </v-step>
        </transition>
      </template>
    </v-tour>
  </div>
</template>

<script>
export default {
  name: 'my-tour',
  data () {
    return {
      steps: [
        {
          target: '#v-step-0',
          content: `Discover <strong>Vue Tour</strong>!`
        },
        {
          target: '#v-step-0-1',
          content: `Created by <a href="https://github.com/pulsardev" target="_blank" rel="noopener">Pulsar</a> and its <a href="https://github.com/pulsardev/vue-tour/graphs/contributors" target="_blank" rel="noopener">contributors</a>.`
        },
        {
          target: '#v-step-1',
          header: {
            title: 'Vue Tour'
          },
          content: 'An awesome plugin made with Vue.js!'
        },
        {
          target: '#v-step-2',
          content: 'Try it, you\'ll love it!<br>You can put HTML in the steps and completely customize the DOM to suit your needs.',
          params: {
            placement: 'top'
          }
        },
        {
          target: '#v-step-3',
          params: {
            placement: 'left'
          }
        },
        {
          target: '#v-step-4',
          content: 'Change the user dom for the incoming step'
        }
      ],
      callbacks: {
        onBeforePreviousStep: this.myCustomBeforePreviousStepCallback,
        onBeforeNextStep: this.myCustomBeforeNextStepCallback,
        onPreviousStep: this.myCustomPreviousStepCallback,
        onNextStep: this.myCustomNextStepCallback
      }
    }
  },
  mounted: function () {
    this.$tours['myTour'].start()

    // A dynamically added onStop callback
    this.callbacks.onStop = () => {
      this.$parent.closeSideBar()
      document.querySelector('#v-step-0').scrollIntoView({ behavior: 'smooth' })
    }
  },
  methods: {
    nextStep () {
      this.$tours['myTour'].nextStep()
    },
    showLastStep () {
      this.$parent.openSideBar()
      setTimeout(() => {
        this.$tours['myTour'].currentStep = this.steps.length - 1
      }, 300)
    },
    myCustomPreviousStepCallback (currentStep) {
      console.log('[Vue Tour] A custom previousStep callback has been called on step ' + (currentStep + 1))
    },
    myCustomNextStepCallback (currentStep) {
      console.log('[Vue Tour] A custom nextStep callback has been called on step ' + (currentStep + 1))

      if (currentStep === 1) {
        console.log('[Vue Tour] A custom nextStep callback has been called from step 2 to step 3')
      }
    },
    myCustomBeforeNextStepCallback (nextStep, currentStep) {
      console.log('[Vue Tour] A custom beforeNextStep callback has been called on step ' + (currentStep + 1))

      if (currentStep === 4) {
        this.$parent.openSideBar()

        setTimeout(() => {
          nextStep()
        }, 300)

        return
      }

      nextStep()
    },
    myCustomBeforePreviousStepCallback (previousStep, currentStep) {
      console.log('[Vue Tour] A custom beforePreviousStep callback has been called on step ' + (currentStep + 1))

      if (currentStep === 5) {
        this.$parent.closeSideBar()
      }

      previousStep()
    }
  }
}
</script>

<style scoped>
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
