<template>
  <div class="listing-wrapper">
    <div class="inputs">
      <div>
        <label for="api-key">API Key</label>
        <input id="api-key" v-model="api_key" size="30" />
        <span>Quota: {{ quota_remaining }}</span>
      </div>
      <div class="dropdowns">
        <select id="select-market" disabled>
          <option value="h2h">Money Line</option>
          <option value="spreads">Spreads</option>
          <option value="totals">Over/Under</option>
        </select>
        <select id="select-country" v-model="locale">
          <option value="uk">UK</option>
          <option value="us">USA</option>
          <option value="eu">Europe</option>
          <option value="au">Australia</option>
        </select>
      </div>
    </div>
    <ul class="sports-tabs">
      <li v-for="(sport, index) in sports" :key="index">
        <button
          @click.prevent="getEvents($event)"
          v-bind:class="{ active: sport.active }"
        >
          {{ sport.title }}
        </button>
      </li>
    </ul>
    <div class="listing-results-wrapper">
      <p class="error-message" v-if="results.length === 0">
        Have you tried clicking on something? 🤨
      </p>
      <OddItem
        v-for="(result, index) in results"
        :key="index"
        :match_odds="result"
        :market="'h2h'"
      />
    </div>
  </div>
</template>

<script>
import OddItem from "@/components/OddItem.vue";
import { base_url } from "@/lib/api";
import axios from "axios";

export default {
  name: "OddsListing",
  components: { OddItem },
  data() {
    return {
      sports: [],
      locale: "uk",
      markets: "h2h",
      results: [],
      api_key: "",
      quota_remaining: 0,
    };
  },
  async created() {
    await this.getSports();
  },
  methods: {
    async getSports() {
      this.sports = [];
      const whitelisted_groups = [
        "American Football",
        "Baseball",
        "Basketball",
        "Mixed Martial Arts",
        "Rugby League",
      ];
      // Get all sports listing.
      const response = await axios.get(base_url + "/sports", {
        params: {
          apiKey: this.api_key,
        },
      });
      const sports = response.data;
      const sports_by_groups = {};
      sports.forEach((element) => {
        if (sports_by_groups[element.group] == null) {
          sports_by_groups[element.group] = [];
        }
        sports_by_groups[element.group].push(element);
      });
      Object.keys(sports_by_groups).forEach((key) => {
        if (whitelisted_groups.indexOf(key) !== -1) {
          this.sports.push({
            title: key,
            active: false,
            leagues: Object.values(sports_by_groups[key]),
          });
        }
      });
    },
    async getEvents(e) {
      this.results = [];
      const sport_group = e.target.innerText;
      this.sports.forEach((element) => {
        if (element.title === sport_group) {
          element.active = true;
          element.leagues.forEach(async (league) => {
            try {
              const response = await axios.get(
                base_url + "/sports/" + league.key + "/odds/",
                {
                  params: {
                    apiKey: this.api_key,
                    regions: this.locale,
                    markets: this.markets,
                  },
                }
              );
              this.results.push(...response.data);
              this.quota_remaining = response.headers["x-requests-remaining"];
            } catch (err) {
              console.error(err.response);
            }
          });
        } else {
          element.active = false;
        }
      });
    },
  },
  watch: {
    api_key() {
      this.getSports();
    },
    locale() {
      this.getSports();
    },
  },
};
</script>

<style lang="scss" scoped>
.listing-wrapper {
  max-width: 1280px;
  min-width: 70%;
}

.inputs {
  display: flex;
  margin-bottom: 24px;
  align-items: center;
  justify-content: space-between;

  div {
    display: flex;
    align-items: center;
  }
  label {
    margin-right: 12px;
  }
  input {
    height: 2rem;
    padding: 1rem 0.5rem;
    margin-right: 12px;
  }
  span {
    height: 2rem;
  }
}

.dropdowns {
  display: flex;
  justify-content: flex-end;
  select {
    font-size: medium;
    width: 10rem;
    height: 2rem;
    padding: 0 0.5rem;
    margin-left: 8px;
  }
}

.sports-tabs {
  display: flex;
  list-style: none;
  padding: 0;
  border-bottom: 1px #282828 solid;

  li {
    border-top: 1px #282828 solid;
    border-right: 1px #282828 solid;
    border-left: 1px #282828 solid;
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;

    button {
      padding: 12px 16px;
      border: none;
      background-color: transparent;
      color: hsla(37, 69%, 58%, 1);
      transition: 0.4s;

      &:hover {
        background-color: hsla(45, 85%, 66%, 0.4);
        cursor: pointer;
      }

      &.active {
        background-color: hsla(45, 55%, 50%, 1);
        color: white;
        font-weight: bolder;
      }
    }

    &:not(:last-child) {
      margin-right: 4px;
    }
  }
}

.error-message {
  font-size: x-large;
  padding: 2rem 0;
}
</style>
