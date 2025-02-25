<template>
  <v-dialog v-model="dialog" width="45rem" persistent :attach="attach" content-class="file-correction-dialog">
    <v-card>
      <v-card-title class="pt-8">Correction Filing</v-card-title>

      <v-card-text class="font-15">
        <p>Select who caused the correction. If client, completing party information and certification
          will be required. If staff, completing party information and certification will not be required.</p>
        <v-radio-group v-model="correctionType">
          <v-radio id="correct-client-radio" class="mb-0 pt-2" label="Client Error" :value="CorrectionTypes.CLIENT" />
          <v-radio id="correct-staff-radio" class="mb-0 pt-2" label="Staff Error" :value="CorrectionTypes.STAFF" />
        </v-radio-group>
        <template v-if="!hasChosenCorrection">
          <p class="font-15 option-error">Choose one option to proceed</p>
        </template>
      </v-card-text>

      <v-card-actions class="py-10">
        <v-btn outlined large
          class="mr-2"
          color="primary"
          id="dialog-exit-button"
          @click="exit()"
          >Cancel</v-btn>
        <v-btn depressed large
          color="primary"
          id="dialog-start-button"
          @click="checkToStart()"
        >Start Correction</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { Component, Vue, Prop, Emit, Watch } from 'vue-property-decorator'
import { CorrectionTypes } from '@/enums'

@Component({})
export default class FileCorrectionDialog extends Vue {
  // enums for template
  readonly CorrectionTypes = CorrectionTypes

  // local variables
  private hasChosenCorrection = true
  protected correctionType = null as CorrectionTypes

  // Prop to display the dialog.
  @Prop() readonly dialog: boolean

  // Prop to provide attachment selector.
  @Prop() readonly attach: string

  /** Check off buttons to confirm correction type */
  protected checkToStart () {
    if (this.correctionType === null) {
      this.hasChosenCorrection = false
    } else {
      this.hasChosenCorrection = true
      this.emitRedirect(this.correctionType)
    }
  }

  /** Called when payment option (radio group item) has changed. */
  @Watch('correctionType')
  private onCorrectionTypeChanged (val: string): void {
    switch (val) {
      case CorrectionTypes.CLIENT:
        this.correctionType = CorrectionTypes.CLIENT
        this.hasChosenCorrection = true
        break
      case CorrectionTypes.STAFF:
        this.correctionType = CorrectionTypes.STAFF
        this.hasChosenCorrection = true
        break
      default:
        break
    }
  }

  // Pass click event to parent.
  @Emit() protected exit () {
    this.hasChosenCorrection = true
    this.correctionType = null
  }

  /**
   * Emits event to Edit UI for correction.
   * Redirect to start correction.
   */
  @Emit('redirect')
  private emitRedirect (correctionType: CorrectionTypes): void { }
}
</script>

<style lang="scss" scoped>
@import "@/assets/styles/theme.scss";

::v-deep .v-dialog {
  .v-card {
    .v-card__title {
      color: black;
      background: white;
      font-weight: bold;
    }
    .v-card__actions {
      justify-content: center;
    }
    .option-error {
      color: $app-red;
    }
  }
}
</style>
