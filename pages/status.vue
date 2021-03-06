<template>
  <div>
    <h1>How Candidates can Support Net Neutrality</h1>
    <p>
      About 1154 candidates are running for election in 470 Congressional races
      during the 2018 midterm elections … and we&rsquo;re doing our best to
      keep track of all of them!
    </p>
    <p>
      When a candidate has publicly expressed support for restoring strong net
      neutrality protections, we consider that candidate to
      <strong class="text-loud">support</strong> net neutrality. If a candidate
      has failed to publicly state a position on net neutrality, we consider
      that candidate to have <strong class="text-loud">no stance</strong>. When
      a candidate opposes using the CRA to restore net neutrality, we consider
      that candidate to <strong class="text-loud">oppose</strong> net neutrality.
    </p>
    <p>
      For sitting members of Congress, it&rsquo;s important to remember that
      incumbent Senators have already voted on the CRA, while members of the
      House of Representatives have had the opportunity to bring the CRA to a
      vote, but have failed to do so.  Some of these lawmakers are running for
      re-election and have stated that they support net neutrality. But without
      strong actions to back up their stated position, we have no choice but to
      judge them on their resistance to protecting net neutrality.
    </p>
    <p>
      If you are a candidate (or represent a candidate), you can submit a
      request to update their stance on scorecard by  filling out the form below.
      Make sure to include a URL showing proof of the candidate&rsquo;s position
      on the CRA to restore net neutrality or we will be unable to make the
      change. For example this could be a
      <a href="https://twitter.com/laurenbaer/status/1006596999951441920" target="_blank">tweet</a>,
      <a href="https://www.phillipsforcongress.org/net-neutrality/" target="_blank">
        statement on the candidate&rsquo;s website,</a>
      or an
      <a href="https://www.nbcwashington.com/news/local/This-is-What-Local-Politicians-are-Doing-About-Net-Neutrality--462615043.html" target="_blank">
      article with a quote</a>.
    </p>

    <p class="text-warn sml-push-y4" v-if="errorMessage">{{ errorMessage }}</p>

    <h3 class="text-success sml-push-y4" v-if="hasSubmitted">
      Thanks for sending us updated information on {{ candidateName }}&rsquo;s
      position on net neutrality!
    </h3>

    <form v-if="!hasSubmitted" @submit.prevent="formSubmit" class="sml-push-y4">
      <input type="text"
             v-model="candidateName"
             placeholder="Candidate Name*"
             required
             :disabled="isArchived" />
      <input type="email"
             v-model="email"
             class="sml-push-y2"
             placeholder="Your Email*"
             required
             :disabled="isArchived" />
      <input type="tel"
             v-model="phone"
             class="sml-push-y2"
             placeholder="Your Phone Number*"
             required
             :disabled="isArchived" />

      <div class="flex-row sml-push-y2">
        <select v-model="state" required :disabled="isArchived">
          <option :value="null">Select a state*</option>
          <option v-for="(name, code) in stateOptions" :key="`state-${code}`" :value="code">
            {{ name }}
          </option>
        </select>

        <select v-model="district" :disabled="isArchived">
          <option :value="null">Select a district</option>
          <option value="at-large">At Large</option>
          <option v-for="option in districtOptions" :key="`district-${option}`" :value="option">
            {{ option }}
          </option>
        </select>
      </div> <!-- .flex-row -->

      <select v-model="status" class="sml-push-y2" required="" :disabled="isArchived">
        <option :value="null">Select candidate&rsquo;s position on net neutrality*</option>
        <option v-for="option in statusOptions" :key="`status-${option}`" :value="option">
          {{ option }}
        </option>
      </select>

      <input type="text"
             v-model="url"
             class="sml-push-y2"
             placeholder="Link to a source for the candidate's stance*"
             required
             :disabled="isArchived" />
      <div class="checkbox sml-push-y2">
        <input type="checkbox" id="authorized" v-model="authorized" required :disabled="isArchived">
        <label for="authorized">I am authorized to make this correction on behalf of the candidate.</label>
      </div> <!-- .checkbox -->
      <button class="btn btn-block sml-push-y2" :disabled="isLoading || isArchived ">
        <span v-if="isLoading">Loading...</span>
        <span v-else>Submit</span>
      </button>
    </form>
  </div>
</template>

<script>
import axios from 'axios'
import { mapState } from 'vuex'
import US_STATES from '~/assets/data/states'

export default {
  layout: 'article',

  data () {
    return {
      isLoading: false,
      errorMessage: null,
      hasSubmitted: false,
      candidateName: null,
      email: null,
      phone: null,
      state: null,
      district: null,
      districtOptions: Array.from(new Array(50),(val,index)=>index+1),
      status: null,
      statusOptions: [ 'Supports', 'No Stance', 'Opposes'],
      url: null,
      authorized: null
    }
  },

  computed: {
    ...mapState(['isArchived']),

    stateOptions() { return US_STATES }
  },

  methods: {
    async formSubmit() {
      this.hasSubmitted = false
      this.isLoading = true
      this.errorMessage = null

      try {
        const { data } = await axios.post(
          'https://sheetz.fftf.xyz/sheets/1GULBbKsoCwO-8nM-YkGHrRD-tZSUB92K0oMtv-Vx1Zs/rows',
          {
            name: this.candidateName,
            email: this.email,
            phone: this.phone,
            state: this.state,
            district: this.district ? this.district : '',
            status: this.status,
            proofurl: this.url
          }
        )
        this.hasSubmitted = true
      }
      catch (error) {
        this.errorMessage = "Oh no, something went wrong! Please try again."
      }

      this.isLoading = false
    }
  }
}
</script>
