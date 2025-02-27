<template lang="pug">
  .credit-score
    p.font-weight-medium Sentiment Score
    v-row
      v-col(cols="5")
        //- Meter
        v-card.fill-height.rounded-lg.py-2.px-3.mb-3(outlined)
          ApexCharts(type="radialBar" :options="chartOptions" :series="series")
          v-divider.my-2
          p.text-center.mb-0.primary--text.font-weight-medium Status
          h2.text-center.mb-4(:class="getCreditColor(issue.sentimentScore ?? 9)") {{ getCreditStatus(issue.sentimentScore ?? 9) }}

          //- Key issue
          v-card.rounded-lg(outlined)
            v-card.px-1.py-2.d-flex.rounded-lg.background.border(elevation="0")
              v-icon.primary--text.pa-2(small) mdi-alert-circle
              .d-grid.pl-1
                p.mb-1.font-weight-medium.primary--textbody-2.text-decoration-underline Key Issue
                p.mb-0.mt-n1.body-2.darkGrey--text {{ issue.key_issue ?? '-' }}

      //- Score percentage
      v-col(cols="7")
        v-card.fill-height.rounded-lg.pa-3(outlined)
          v-col.pb-1.px-2
            v-card.pa-2.d-flex.rounded-lg.fill-height.align-center(outlined)
              v-icon.primary--text.pa-2.mr-2 mdi-lightbulb-on-outline
              .d-grid.pl-1.darkGrey--text.header Recommendation
                p.mb-0.mt-n1.value.darkGrey2--text {{ issue.recommendation.focus ?? '-' }}
          v-col.pb-1.px-2
            v-card.pa-2.rounded-lg.fill-height.align-center(outlined)
              .d-flex.align-center
                v-icon.primary--text.pa-2.mr-2 mdi-cogs
                .d-grid.pl-1.darkGrey--text.header Suggested Features
              .d-flex.flex-column.pa-2.mb-2.rounded.bg--background2(v-for="feature in issue.recommendation.suggested_features")
                span {{feature}}

</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'IssueAnalysis',
  components: {
  },
  props: {
    issue: null
  },
  data () {
    return {
      chartOptions: null,
      creditScoreValue: [
        {
          name: 'Payment History (45%)',
          per: null
        },
        {
          name: 'Amount Owed (20%)',
          per: null
        },
        {
          name: 'Credit History Length (7%)',
          per: null
        },
        {
          name: 'Credit Mix (14%)',
          per: null
        },
        {
          name: 'New Credit (14%)',
          per: null
        }
      ]
    }
  },
  computed: {
    ...mapGetters({
    }),
    series() {
      // Calculate series only if analysisResults and overall_average_sentiment_score are defined
      return this.issue.sentimentScore
          ? [((this.issue.sentimentScore || 0.75) / 0.85) * 100]
          : [0];
    },
  },

  created () {
    // console.log(this.$route.params)
    // this.series = [((this.issue.sentimentScore ?? 90) / 100) * 100]
    // this.series = [((this.customer.creditScore ?? 750) / 850) * 100]
    this.creditScoreValue[0].per = this.issue.payment_history
    this.creditScoreValue[1].per = this.issue.amount_owed
    this.creditScoreValue[2].per = this.issue.credit_history_length
    this.creditScoreValue[3].per = this.issue.credit_mix
    this.creditScoreValue[4].per = this.issue.new_credit

    this.chartOptions = {
      chart: {
        type: 'radialBar',
        offsetY: -10,
        sparkline: {
          enabled: true
        }
      },
      plotOptions: {
        radialBar: {
          hollow: {
            size: '70%',
            margin: -20,
            image: require('../../assets/img/speedometer.svg'),
            imageWidth: 64,
            imageHeight: 64,
            imageClipped: false
          },
          startAngle: -90,
          endAngle: 90,
          track: {
            background: '#f9f4f4',
            strokeWidth: '70%',
            margin: 5, // margin is in pixels
            dropShadow: {
              enabled: true,
              top: 0,
              left: 0,
              color: '#c1c1c1',
              opacity: 0.5,
              blur: 1
            }
          },
          dataLabels: {
            name: {
              show: true,
              offsetY: -40,
              fontSize: '22px',
              color: '#BB0000'
            },
            value: {
              show: false
            }
          }
        }
      },
      grid: {
        padding: {
          top: -10
        }
      },
      fill: {
        type: 'gradient',
        gradient: {
          type: 'horizontal',
          shade: 'light',
          shadeIntensity: 0.4,
          inverseColors: false,
          opacityFrom: 1,
          opacityTo: 1,
          stops: [0, 10, 53, 91],
          colorStops: [
            {
              offset: -2.35,
              color: '#BB0000',
              opacity: 1
            },
            {
              offset: 60.52,
              color: '#FDA42E',
              opacity: 1
            },
            {
              offset: 84.99,
              color: '#3D9970',
              opacity: 1
            }
          ]
        }
      },
      labels: [(this.issue.sentimentScore ?? 9).toFixed(1)]
    }
  },
  methods: {
    getCreditStatus (score) {
      if (score >= 0.6) {
        return 'Positive'
      } else if (score >= 0.3) {
        return 'Neutral'
      } else {
        return 'Negative'
      }
    },
    getCreditColor (score) {
      if (score >= 0.8) {
        return 'excellent--text'
      } else if (score >= 0.5) {
        return 'good--text'
      } else {
        return 'weak--text'
      }
    },
    getProgressBarColor (value) {
      const color = this.$vuetify.theme.themes.light.primary
      const opacity = (value) / 100 + 0.1 // Assuming value is between 0 and 100

      // Parse the color to get the RGB values
      const matches = color.match(/^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i)
      const r = parseInt(matches[1], 16)
      const g = parseInt(matches[2], 16)
      const b = parseInt(matches[3], 16)

      // Return the color with the adjusted opacity
      return `rgba(${r}, ${g}, ${b}, ${opacity})`
    }
  }
}
</script>

<style scoped>
:deep(.v-progress-linear) {
  border: solid 6px #d6dceb;
}

.header{
  font-size: 14px;
}

.value{
  font-size: 16px;
}

.bg--background2 {
  background-color: #F2E7E7;
}
</style>
