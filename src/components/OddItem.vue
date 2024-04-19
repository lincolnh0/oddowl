<template>
  <div class="odd-item-wrapper" v-bind:class="{ arbitrage: is_arbitrage, hidden:!is_arbitrage }">
    <div class="odd-item-main" @click.prevent="expanded = !expanded">
      <div class="away-team">
        <p class="team-name">{{ away_team }}</p>
        <p class="odd-range">({{ min_away_odds }} - {{ max_away_odds }})</p>
      </div>
      <div class="center">
        <span
          v-if="
            probability < highlight_threshold &&
            max_home_bookmaker !== max_away_bookmaker
          "
          >👑</span
        >
        <p class="versus">vs</p>
        <p class="sport-title">{{ sport_title }}</p>
      </div>
      <div class="home-team">
        <p class="team-name">{{ this.home_team }}</p>
        <p class="odd-range">({{ min_home_odds }} - {{ max_home_odds }})</p>
      </div>
    </div>
    <div class="odd-item-expanded" v-bind:class="{ visible: expanded }">
      <div class="away-odds">
        <ul class="bookmaker-list">
          <li
            v-bind:class="{ max: bookmaker.price === this.max_away_odds }"
            v-for="(bookmaker, index) in away_bookmakers"
            :key="index"
          >
            {{ bookmaker.title }} - {{ bookmaker.price }}
          </li>
        </ul>
      </div>
      <div class="stats">
        <span class="heading">Arbitrage Calculator</span>
        <span>Probability: {{ probability }}%</span>
        <div class="stake-row">
          <label for="total-stake">Total stake</label>
          <label for="round-to">Round to</label>
        </div>
        <div class="stake-row">
          <input
            id="total-stake"
            type="number"
            min="0"
            step="0.01"
            v-model="stake"
          />
          <input
            id="round-to"
            type="number"
            min="0"
            step="0.01"
            v-model="rounding"
          />
        </div>
        <div class="stake-row">
          <span>{{ away_stake }}</span>
          <span>{{ home_stake }}</span>
        </div>
        <span class="heading">Profit</span>
        <div class="stake-row">
          <span>{{ away_profit }}</span>
          <span>{{ home_profit }}</span>
        </div>
      </div>
      <div class="home-odds">
        <ul class="bookmaker-list">
          <li
            v-bind:class="{ max: bookmaker.price === this.max_home_odds }"
            v-for="(bookmaker, index) in home_bookmakers"
            :key="index"
          >
            {{ bookmaker.price }} - {{ bookmaker.title }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "OddItem",
  props: ["match_odds", "market"],
  data() {
    return {
      highlight_threshold: 96,
      stake: 100,
      rounding: 0.01,
      away_team: "",
      home_team: "",
      sport_title: "",
      min_away_odds: 0,
      max_away_odds: 0,
      min_home_odds: 0,
      max_home_odds: 0,
      bookmakers: [],
      away_bookmakers: [],
      home_bookmakers: [],
      max_home_bookmaker: "",
      max_away_bookmaker: "",
      expanded: false,
    };
  },
  created() {
    this.away_team = this.match_odds.away_team;
    this.home_team = this.match_odds.home_team;
    this.bookmakers = this.match_odds.bookmakers;
    this.sport_title = this.match_odds.sport_title;
    this.bookmakers.forEach((bookmaker) => {
      bookmaker.markets.forEach((market) => {
        if (market.key === this.market) {
          if (market.outcomes.length > 2) {
            console.log(bookmaker);
          }
          market.outcomes.forEach((outcome) => {
            if (outcome.name === this.away_team) {
              this.min_away_odds =
                this.min_away_odds === 0 ? outcome.price : this.min_away_odds;
              this.min_away_odds = Math.min(outcome.price, this.min_away_odds);
              this.max_away_odds = Math.max(outcome.price, this.max_away_odds);
              if (this.max_away_odds === outcome.price) {
                this.max_away_bookmaker = bookmaker.title;
              }

              this.away_bookmakers.push({
                title: bookmaker.title,
                key: bookmaker.key,
                price: outcome.price,
                last_update: bookmaker.last_update,
              });
            } else if (outcome.name === this.home_team) {
              this.min_home_odds =
                this.min_home_odds === 0 ? outcome.price : this.min_home_odds;
              this.min_home_odds = Math.min(outcome.price, this.min_home_odds);
              this.max_home_odds = Math.max(outcome.price, this.max_home_odds);
              if (this.max_home_odds === outcome.price) {
                this.max_home_bookmaker = bookmaker.title;
              }
              this.home_bookmakers.push({
                title: bookmaker.title,
                key: bookmaker.key,
                price: outcome.price,
                last_update: bookmaker.last_update,
              });
            }
          });
        }
      });
    });
  },
  computed: {
    is_arbitrage() {
      return 1 / this.max_away_odds + 1 / this.max_home_odds < 1;
    },
    probability() {
      return ((1 / this.max_away_odds + 1 / this.max_home_odds) * 100).toFixed(
        2
      );
    },
    home_stake() {
      if (this.max_home_odds === 0) return 0;
      return (
        Math.round(
          (this.stake * (100 / this.max_home_odds)) /
            this.probability /
            this.rounding
        ) * this.rounding
      ).toFixed(2);
    },
    away_stake() {
      if (this.max_away_odds === 0) return 0;
      return (
        Math.round(
          (this.stake * (100 / this.max_away_odds)) /
            this.probability /
            this.rounding
        ) * this.rounding
      ).toFixed(2);
    },
    home_profit() {
      return (this.home_stake * this.max_home_odds - this.stake).toFixed(2);
    },
    away_profit() {
      return (this.away_stake * this.max_away_odds - this.stake).toFixed(2);
    },
  },
};
</script>

<style lang="scss" scoped>
.odd-item-wrapper {
  border-bottom: 1px solid #282828;
  background-color: var(--color-background);

  &.arbitrage {
    background-color: hsla(120, 93%, 79%, 1);
  }

  &.hidden {
    display: none;
  }
}

.odd-item-main {
  cursor: pointer;
  display: flex;
  width: 100%;
  justify-content: space-between;
  padding: 24px;
  text-align: center;
  align-items: center;
  font-size: small;

  @media (min-width: 800px) {
    font-size: large;
  }
}

.odd-item-expanded {
  max-height: 0;
  transition: 0.4s ease-in-out;
  overflow: hidden;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 24px;
  font-size: small;

  .bookmaker-list {
    list-style: none;
    padding: 0;

    li.max {
      font-weight: bolder;
    }
  }

  &.visible {
    padding: 24px;
    max-height: 80vh;
  }

  @media (min-width: 800px) {
    font-size: medium;

    .bookmaker-list {
      text-align: center;
    }
  }
}

.stats {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 0;

  .heading {
    font-weight: bolder;
    border-bottom: 1px solid #282828;
    padding: 0 8px;
    margin-bottom: 16px;
  }

  span {
    margin: 8px 0;
  }

  .stake-row {
    display: flex;
    align-items: center;
    justify-content: center;

    input {
      max-width: 20%;
      padding: 8px;
      text-align: center;
    }

    span {
      margin: 12px 0;
      padding: 0 24px;

      &:first-child {
        border-right: 2px solid #282828;
      }
    }

    label {
      margin: 12px;
    }
  }

  @media (min-width: 800px) {
    max-width: 100%;
    input {
      font-size: large;
    }
  }
}

.away-odds {
  .bookmaker-list {
    text-align: left;
  }
}

.home-odds {
  .bookmaker-list {
    text-align: right;
  }
}

.center,
.home-team,
.away-team,
.home-odds,
.away-odds,
.stats {
  flex: 1;
}

.sport-title {
  font-size: small;
}
</style>
