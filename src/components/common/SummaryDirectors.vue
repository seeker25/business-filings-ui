<template>
  <div id="summary-directors">
    <v-card flat>
      <!-- Current Director List -->
      <ul class="list director-list">
        <v-subheader class="director-header">
          <span>Names</span>
          <span>Delivery Address</span>
          <span v-if="isBComp">Mailing Address</span>
          <span class="header-appointed">Appointed/Elected</span>
        </v-subheader>

        <li class="director-list-item"
          v-for="(director, index) in directorSummary"
          :id="'director-' + director.id"
          :class="{ 'remove' : !isActionable(director)}"
          :key="index"
        >
          <div class="meta-container">
            <label>
              <span>{{director.officer.firstName}} </span>
              <span>{{director.officer.middleInitial}} </span>
              <span>{{director.officer.lastName}}</span>
              <div class="director-status">
                <v-scale-transition>
                  <v-chip x-small label color="blue" text-color="white"
                    v-show="isNew(director) && !director.cessationDate"
                  >
                    New
                  </v-chip>
                </v-scale-transition>
                <v-scale-transition>
                  <v-chip x-small label text-color="rgba(0,0,0,.38)"
                    v-show="!isActionable(director)"
                  >
                    Ceased
                  </v-chip>
                </v-scale-transition>
                <v-scale-transition>
                  <v-chip x-small label color="blue lighten-2" text-color="white"
                    v-show="isNew(director) && director.cessationDate"
                  >
                    Appointed and Ceased
                  </v-chip>
                </v-scale-transition>
                <v-scale-transition>
                  <v-chip x-small label color="blue" text-color="white"
                    v-show="isNameChanged(director)"
                  >
                    Name Changed
                  </v-chip>
                </v-scale-transition>
                <v-scale-transition>
                  <v-chip x-small label color="blue" text-color="white"
                    v-show="isAddressChanged(director)"
                  >
                    Address Changed
                  </v-chip>
                </v-scale-transition>
              </div>
            </label>

            <div class="meta-container__inner">
              <v-expand-transition>
                <div class="director-info">
                  <div class="address">
                    <base-address :address="director.deliveryAddress" />
                  </div>

                  <div class="address same-address" v-if="isBComp">
                    <span v-if="isSame(director.deliveryAddress, director.mailingAddress)">
                      Same as Delivery Address
                    </span>
                    <base-address v-else :address="director.mailingAddress" />
                  </div>

                  <div class="director_dates">
                    <div class="director_dates__date">{{ director.appointmentDate }}</div>
                    <div v-if="director.cessationDate">Ceased</div>
                    <div class="director_dates__date">{{ director.cessationDate }}</div>
                  </div>
                </div>
              </v-expand-transition>
            </div>
          </div>
        </li>
      </ul>
    </v-card>

    <br>

    <!-- Ceased Directors List -->
    <v-btn text small v-if="directorsCeased.length > 0" @click="expand = !expand" class="cease-btn">
      <v-icon>{{ dropdownIcon }}</v-icon>
      <span>Hide Ceased Directors</span>
    </v-btn>
    <v-card flat>
      <v-expand-transition>
        <ul class="list director-list" v-show="expand">
          <li class="director-list-item"
            v-for="(director, index) in directorsCeased"
            :id="'director-' + director.id"
            :class="{ 'remove' : !isActive(director) || !isActionable(director)}"
            :key="index"
          >
            <div class="meta-container">
              <label>
                <span>{{director.officer.firstName}} </span>
                <span>{{director.officer.middleInitial}} </span>
                <span>{{director.officer.lastName}}</span>
                <div class="director-status">
                  <v-scale-transition>
                    <v-chip x-small label color="blue" text-color="white"
                      v-show="isNew(director) && !director.cessationDate"
                    >
                      New
                    </v-chip>
                  </v-scale-transition>
                  <v-scale-transition>
                    <v-chip x-small label text-color="rgba(0,0,0,.38)"
                      v-show="!isActive(director) || !isActionable(director)"
                    >
                      Ceased
                    </v-chip>
                  </v-scale-transition>
                  <v-scale-transition>
                    <v-chip x-small label color="blue lighten-2" text-color="white"
                      v-show="isNew(director) && director.cessationDate"
                    >
                      Appointed and Ceased
                    </v-chip>
                  </v-scale-transition>
                  <v-scale-transition>
                    <v-chip x-small label color="blue" text-color="white"
                      v-show="isNameChanged(director)"
                    >
                      Name Changed
                    </v-chip>
                  </v-scale-transition>
                  <v-scale-transition>
                    <v-chip x-small label color="blue" text-color="white"
                      v-show="isAddressChanged(director)"
                    >
                      Address Changed
                    </v-chip>
                  </v-scale-transition>
                </div>
              </label>

              <div class="meta-container__inner">
                <v-expand-transition>
                  <div class="director-info">
                    <div class="address">
                      <base-address :address="director.deliveryAddress" />
                    </div>
                    <div class="address same-address" v-if="isBComp">
                      <span v-if="isSame(director.deliveryAddress, director.mailingAddress)">
                        Same as Delivery Address
                      </span>
                      <base-address v-else :address="director.mailingAddress" />
                    </div>
                    <div class="director_dates">
                      <div class="director_dates__date">{{ director.appointmentDate }}</div>
                      <div v-if="director.cessationDate">Ceased</div>
                      <div class="director_dates__date">{{ director.cessationDate }}</div>
                    </div>
                  </div>
                </v-expand-transition>
              </div>
            </div>
          </li>
        </ul>
      </v-expand-transition>
    </v-card>
  </div>
</template>

<script lang="ts">
// Libraries
import { Component, Mixins, Prop, Watch } from 'vue-property-decorator'
import { Getter } from 'vuex-class'

// Components
import BaseAddress from 'sbc-common-components/src/components/BaseAddress.vue'

// Mixins
import { CommonMixin, DateMixin } from '@/mixins'

// Enums
import { Actions } from '@/enums'

// Interfaces
import { DirectorIF } from '@/interfaces'

@Component({
  components: {
    BaseAddress
  }
})
export default class SummaryDirectors extends Mixins(CommonMixin, DateMixin) {
  // Directors array passed into this component.
  @Prop({ default: [] })
  readonly directors: Array<DirectorIF>

  @Getter isBComp!: boolean

  // Local properties
  private directorSummary: Array<DirectorIF> = []
  private directorsCeased: Array<DirectorIF> = []
  private expand: boolean = true

  /**
    * Watcher to keep director lists up to date.
    * - "directorSummary" will contain current directors
    * - "directorsCeased" will contain ceased directors
    */
  @Watch('directors', { deep: true, immediate: true })
  private onDirectorsChanged (val: Array<DirectorIF>): void {
    this.directorSummary = val.filter(d => !d.actions || !d.actions.includes(Actions.CEASED))
    this.directorsCeased = val.filter(d => d.actions && d.actions.includes(Actions.CEASED))
  }

  /**
   * Getter to return the proper icon depending on dropdown state.
   * @returns The icon name.
   */
  private get dropdownIcon (): string {
    return this.expand ? 'mdi-menu-up' : 'mdi-menu-down'
  }

  /**
   * Local helper to check whether a director was appointed (ie, added) in this filing.
   * @param director The director to check.
   * @returns True if director was appointed.
   */
  private isNew (director: DirectorIF): boolean {
    // return director.actions && director.actions.includes(Actions.APPOINTED)
    return director.actions && (director.actions.indexOf(Actions.APPOINTED) >= 0)
  }

  /**
   * Local helper to check whether a director had their address changed.
   * @param director The director to check.
   * @returns True if director had their address changed.
   */
  private isAddressChanged (director: DirectorIF): boolean {
    // return director.actions && director.actions.includes(Actions.ADDRESSCHANGED)
    return director.actions && (director.actions.indexOf(Actions.ADDRESSCHANGED) >= 0)
  }

  /**
   * Local helper to check whether a director had their name changed.
   * @param director The director to check.
   * @returns True if director had their name changed.
   */
  private isNameChanged (director: DirectorIF): boolean {
    // return director.actions && director.actions.includes(Actions.NAMECHANGED)
    return director.actions && (director.actions.indexOf(Actions.NAMECHANGED) >= 0)
  }

  /**
   * Local helper to check whether a director is active (ie, not ceased) in this filing.
   * @param director The director to check.
   * @returns True if director is active.
   */
  private isActive (director: DirectorIF): boolean {
    // return director.actions && director.actions.includes(Actions.CEASED)
    return director.actions && (director.actions.indexOf(Actions.CEASED) < 0)
  }

  /**
   * Local helper to check whether a director is actionable.
   * @param director The director to check.
   * @returns True if director is actionable.
   */
  private isActionable (director: DirectorIF): boolean {
    return (director.isDirectorActionable !== undefined) ? director.isDirectorActionable : true
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/styles/theme.scss';

.v-card {
  line-height: 1.2rem;
  font-size: $px-14;
}

.v-btn {
  margin: 0;
  text-transform: none;
}

ul {
  margin: 0;
  padding: 0;
  list-style-type: none;
}

.meta-container {
  display: flex;
  flex-flow: column nowrap;
  position: relative;

  > label:first-child {
    font-weight: 700;
  }

  &__inner {
    flex: 1 1 auto;
  }
}

@media (min-width: 768px) {
  .meta-container {
    flex-flow: row nowrap;

    > label:first-child {
      flex: 0 0 auto;
      width: 14rem;
    }
  }
}

// List Layout
.list {
  li {
    border-bottom: 1px solid $gray3;
  }
}

.form__row.three-column {
  display: flex;
  flex-flow: row nowrap;
  align-items: stretch;
  margin-right: -0.5rem;
  margin-left: -0.5rem;

  .item {
    flex: 1 1 auto;
    flex-basis: 0;
    margin-right: 0.5rem;
    margin-left: 0.5rem;
  }
}

// Address Block Layout
.address {
  display: flex;
  width: 14rem;
}

.address__row {
  flex: 1 1 auto;
}

// Director Display
.director-info {
  display: flex;
  color: $gray6;

  .status {
    flex: 1 1 auto;
  }
}

// V-chip customization
.v-size--x-small {
  display: table;
  margin-top: 0.5rem;
  text-transform: uppercase;
  font-weight: 700;
}

.remove, .remove .director-info {
  color: $gray5 !important;
}

.director_dates {
  font-size: px-13;
}

.director-header {
  width: 100%;
  padding: 1.25rem;
  display: inline-flex;
  height: 3rem;
  background-color: rgba(77, 112, 147, 0.15);

  span {
    width: 14rem;
    color: $app-almost-black;
    font-size: $px-14;
    font-weight: 600;
    line-height: 1.1875rem;
  }

  .header-appointed {
    width: 11.4rem;
  }
}

.director-list-item {
  padding: 1.25rem;
}

.cease-btn {
  color: $BCgovIconBlue;
}
</style>
