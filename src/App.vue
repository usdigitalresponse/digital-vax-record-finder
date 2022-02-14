<template>
  <div id="app">
    <div class="container pt-4">
      <div class="row justify-content-md-center">
        <div class="col-lg-6">
          <header>
            <h1>Digital COVID-19 vaccine record finder</h1>
            <h2 class="text-muted">Find out if you can get a secure digital record of your COVID-19 vaccine</h2>
            <hr>
          </header>

          <main>
            <!-- state question card -->
            <div class="state-question-card" v-if="currentCard === 'state-question'">
              <h3>Which state or territory were you vaccinated in?</h3>
              <div class="mb-3">
                <!-- <vue-typeahead-bootstrap
                  :data="stateProviders"
                  v-model="selectedStateProvider"
                  placeholder="Start typing a name..."
                /> -->

                <!-- <label for="exampleDataList" class="form-label">Datalist example</label> -->
                <input class="form-control" list="datalistOptions" id="exampleDataList" placeholder="Type to search..."
                       v-model="selectedStateProvider"
                >
                <datalist id="datalistOptions">
                  <option v-for="stateProvider in [...stateProviders].sort()"
                          :value="stateProvider"
                          :key="stateProvider"
                  />
                </datalist>
              </div>

              <div class="mt-4">
                <button type="submit" class="btn btn-primary" @click="didClickStateProviderNext">Next</button>
              </div>
            </div>
            
            <!-- state success card -->
            <div class="state-success-card" v-if="currentCard === 'state-success'">
              <h3>Great! {{ selectedStateProvider }} offers digital vaccine records.</h3>

              <p>
                Learn more at

                <a :href="urlForSelectedStateProvider" target="_blank">
                  {{ urlForSelectedStateProvider }}
                </a>
              </p>

              <div class="mt-4">
                <button type="submit" class="btn btn-secondary" @click="didClickBackFromSuccess">Back</button>
                <!-- <button type="submit" class="btn btn-primary" @click="didClickStateProviderNext">Click Here to Register</button> -->
              </div>
            </div>

            <!-- retail success card -->
            <!-- TODO this is basically a dupe of the state success card -->
            <div class="retail-success-card" v-if="currentCard === 'retail-success'">
              <h3>Great! {{ selectedRetailProvider }} offers digital vaccine records.</h3>

              <p>
                Learn more at

                <a :href="urlForSelectedRetailProvider" target="_blank">
                  {{ urlForSelectedRetailProvider }}
                </a>
              </p>

              <div class="mt-4">
                <button type="submit" class="btn btn-secondary" @click="didClickBackFromSuccess">Back</button>
              </div>
            </div>

            <!-- retail question card -->
            <div class="retail-question-card" v-if="currentCard === 'retail-question'">
              <h3>Were you vaccinated at one of these pharmacies or stores?</h3>
              <div class="form-check" v-for="(retailProvider, index) in retailProviderDefs" :key="index">
                <input class="form-check-input" type="radio" :name="retailProvider.name" :id="retailProvider.name" :value="retailProvider.name" v-model="selectedRetailProvider">
                <label class="form-check-label" :for="retailProvider.name">
                  {{ retailProvider.name }}
                </label>
              </div>

              <div class="form-check">
                <input class="form-check-input" type="radio" name="none-of-these" id="none-of-these" value="none-of-these" v-model="selectedRetailProvider">
                <label class="form-check-label" for="none-of-these">
                  I was vaccinated somewhere else.
                </label>
              </div>

              <div class="mt-4">
                <button type="submit" class="btn btn-secondary" @click="didClickBackFromRetailProvider">Back</button>
                <button type="submit" class="btn btn-primary" @click="didClickRetailProviderNext">Next</button>
              </div>
            </div>

            <div class="failure-card" v-if="currentCard === 'failure'">
              <h3>We weren't able to find a digital vaccine record program for you, but you may still be eligible.</h3>

              <p>
                Visit the following website and scroll to the "All Issuers" section. If your vaccine provider is listed there,
                contact them for more information about how to get your digital vaccine record.
              </p>

              <p>
                <a href="https://smarthealth.cards/en/issuers.html" target="_blank">https://smarthealth.cards/en/issuers.html</a>
              </p>

              <div class="mt-4">
                <button type="submit" class="btn btn-secondary" @click="didClickBackFromFailure">Back</button>
              </div>
            </div>
          </main>

          <!-- footer -->
          <footer>
            <hr>
            <p class="credit text-muted">
              Made by 
              <a href="https://www.usdigitalresponse.org" target="_blank">U.S. Digital Response</a>
            </p>
          </footer>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      selectedStateProvider: null,
      didSelectStateProvider: false,
      selectedRetailProvider: null,
      didSelectRetailProvider: false,

      stateProviderDefs: [
        // PARTICIPATING
        {
          name: 'California',
          url: 'https://myvaccinerecord.cdph.ca.gov',
        },
        {
          name: 'Colorado',
          url: 'https://myvaccinerecord.cdph.ca.gov',
        },
        {
          name: 'Connecticut',
          url: 'https://ctwiz.dph.ct.gov/ctwiz_public/Application/PublicPortal',
        },
        {
          name: 'Delaware',
          url: 'https://delvax.dhss.delaware.gov/delvax_public/Application/PublicPortal',
        },
        {
          name: 'Hawaii',
          url: 'https://hawaiicovid19.com/smart-health-card/',
        },
        {
          name: 'Illinois',
          url: 'https://idphportal.illinois.gov/s/?language=en_US',
        },
        {
          name: 'Louisiana',
          url: 'https://lawallet.com/covid-19/',
        },
        {
          name: 'Massachusetts',
          url: 'https://myvaxrecords.mass.gov/',
        },
        {
          name: 'Nevada',
          url: 'https://izrecord.nv.gov/public/Application/PublicPortal',
        },
        {
          name: 'New Jersey',
          url: 'https://covid19.nj.gov/pages/vaxrecords',
        },
        {
          name: 'New York',
          url: 'https://covid19vaccine.health.ny.gov/excelsior-pass-and-excelsior-pass-plus',
        },
        {
          name: 'Puerto Rico',
          url: 'https://www.prits.pr.gov/vacu-id#Global-VACU-ID',
        },
        {
          name: 'Rhode Island',
          url: 'https://portal.ri.gov/VaccineRecord/s/',
        },
        {
          name: 'Utah',
          url: 'https://immunize.utah.gov/usiis/',
        },
        {
          name: 'Virginia',
          url: 'https://vase.vdh.virginia.gov/vacapps/f?p=545:1',
        },
        {
          name: 'Washington',
          url: 'https://waverify.doh.wa.gov/',
        },
        // NON-PARTICIPATING
        {
          'name': 'Alabama',
        },
        {
          'name': 'Alaska',
        },
        {
          'name': 'American Samoa',
        },
        {
          'name': 'Arizona',
        },
        {
          'name': 'Arkansas',
        },
        {
          'name': 'District Of Columbia',
        },
        {
          'name': 'Federated States Of Micronesia',
        },
        {
          'name': 'Florida',
        },
        {
          'name': 'Georgia',
        },
        {
          'name': 'Guam',
        },
        {
          'name': 'Idaho',
        },
        {
          'name': 'Indiana',
        },
        {
          'name': 'Iowa',
        },
        {
          'name': 'Kansas',
        },
        {
          'name': 'Kentucky',
        },
        {
          'name': 'Maine',
        },
        {
          'name': 'Marshall Islands',
        },
        {
          'name': 'Maryland',
        },
        {
          'name': 'Michigan',
        },
        {
          'name': 'Minnesota',
        },
        {
          'name': 'Mississippi',
        },
        {
          'name': 'Missouri',
        },
        {
          'name': 'Montana',
        },
        {
          'name': 'Nebraska',
        },
        {
          'name': 'New Hampshire',
        },
        {
          'name': 'New Mexico',
        },
        {
          'name': 'North Carolina',
        },
        {
          'name': 'North Dakota',
        },
        {
          'name': 'Northern Mariana Islands',
        },
        {
          'name': 'Ohio',
        },
        {
          'name': 'Oklahoma',
        },
        {
          'name': 'Oregon',
        },
        {
          'name': 'Palau',
        },
        {
          'name': 'Pennsylvania',
        },
        {
          'name': 'South Carolina',
        },
        {
          'name': 'South Dakota',
        },
        {
          'name': 'Tennessee',
        },
        {
          'name': 'Texas',
        },
        {
          'name': 'Vermont',
        },
        {
          'name': 'Virgin Islands',
        },
        {
          'name': 'West Virginia',
        },
        {
          'name': 'Wisconsin',
        },
        {
          'name': 'Wyoming',
        }
      ],
      retailProviderDefs: [
        {
          name: 'Albertsons',
          url: 'https://www.albertsons.com/myvaccinerecord',
          // helpText: 'Option 2: Text the word “Record” to 1-844-590-0015 using your mobile device. The mobile device must be the same phone number on file with your local pharmacy. Option 3: Visit albertsons.com/MyVaccineRecord. You will be prompted to verify your identity, including your Rx Number. You can find your Rx Number on your vaccination receipt or by calling your local pharmacy.',
        },
        {
          name: 'CVS',
          url: 'https://care.cvs.com/login/',
        },
        {
          name: 'Rite Aid',
          url: 'https://www.riteaid.com/pharmacy/vaccination-digital-proof',
        },
        {
          name: 'Walgreens',
          url: 'https://www.walgreens.com/topic/promotion/covid-vaccine.jsp',
        },
        {
          name: 'Walmart',
          url: 'https://www.walmart.com/cp/digital-vaccine-record/9508321',
        },
      ],
    };
  },
  components: {
    // HelloWorld
  },
  computed: {
    currentCard() {
      let currentCard;

      if (!this.didSelectStateProvider) {
        currentCard = 'state-question';
      } else if (!this.didSelectRetailProvider && !this.participatingStateProviders.includes(this.selectedStateProvider)) {
        currentCard = 'retail-question';
      } else if (this.participatingStateProviders.includes(this.selectedStateProvider)) {
        currentCard = 'state-success';
      } else if (this.retailProviders.includes(this.selectedRetailProvider)) {
        currentCard = 'retail-success';
      } else {
        currentCard = 'failure';
      }

      return currentCard;
    },
    stateProviders() {
      return this.stateProviderDefs.map(provider => provider.name);
    },
    retailProviders() {
      return this.retailProviderDefs.map(provider => provider.name);
    },
    participatingStateProviders() {
      return this.stateProviderDefs
        .filter(provider => !!provider.url)
        .map(provider => provider.name);
    },
    // retailProviders() {
    //   return this.retailProviderDefs.map(provider => provider.name);
    // },
    urlForSelectedStateProvider() {
      const url = this.stateProviderDefs.filter(p => {
        return p.name === this.selectedStateProvider;
      })[0].url;

      return url;
    },
    urlForSelectedRetailProvider() {
      const url = this.retailProviderDefs.filter(p => {
        return p.name === this.selectedRetailProvider;
      })[0].url;

      return url;
    },
  },
  methods: {
    // TODO this is hacky
    didClickBackFromRetailProvider() {
      this.didSelectStateProvider = false;
    },
    didClickBackFromSuccess() {
      if (this.didSelectRetailProvider) {
        this.didSelectRetailProvider = false;
      } else {
        this.didSelectStateProvider = false;
      }
    },
    didClickBackFromFailure() {
      this.selectedRetailProvider = null;
      this.didSelectRetailProvider = false;
    },
    didClickStateProviderNext() {
      // const stateProvider = this.$refs.stateProviderInput.value;
      // this.selectedStateProvider = stateProvider;
      this.didSelectStateProvider = true;
    },
    didClickRetailProviderNext() {
      this.didSelectRetailProvider = true;
    },
  },
};
</script>

<style>
header h1 {
  font-size: 2rem;
}

header h2 {
  font-size: 1rem;
  font-weight: normal;
  margin-top: 0.5rem;
}

header hr {
  margin-bottom: 1.5rem;
}

main h3 {
  font-size: 1.25rem;
  margin-bottom: 1.5rem;
  font-weight: normal;
}

/* HACK */
.btn-secondary {
  margin-right: 1rem;
}

.credit {
  font-size: 0.8rem;
}
</style>
