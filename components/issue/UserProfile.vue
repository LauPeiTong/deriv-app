<template lang="pug">
  v-card.user-profile.shadow.pa-2.rounded-lg(elevation="0")
    v-img.rounded-lg(:src="require(`../../assets/img/backgroundRed.jpg`)" height="150")
      .blurry.full-width

    .d-flex.mt-n14.px-4
      v-avatar.profile-pic(size="120")
        span {{ getInitials(issue.customer.name) }}
      .d-grid.pt-15.pl-4
        p.mb-0.text-h6 {{ issue?.customer.name }}

    .px-4.pt-4.pb-5
      p.font-weight-medium.mb-1 Customer Details
      v-row
        template(v-for="item in basicData")
          v-col.pb-1.px-2(:cols="item.col")
            v-card.px-1.py-2.d-flex.rounded-lg(outlined)
              v-icon.primary--text.pa-2(small) {{ item.icon }}
              .d-grid.pl-1
                .caption.font-weight-light.darkGrey--text {{ item.title }}
                p.mb-0.mt-n1.body-2 {{ item.value }}
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'UserProfile',
  props: {
    issue: null
  },
  data () {
    return {
      basicData: null
    }
  },
  computed: {
    ...mapGetters({
      getIssueById: 'issue/getIssueById'
    })
  },
  created () {
    // console.log(this.$route.params)
    this.basicData = [
      {
        col: 6,
        icon: 'mdi-phone',
        title: 'Phone Number',
        value: this.issue.customer.phone ?? '+6016-382 9423'
      },
      {
        col: 6,
        icon: 'mdi-email',
        title: 'Email Address',
        value: this.issue.customer.email ?? 'raygan@gmail.com'
      },
      {
        col: 4,
        icon: 'mdi-card-account-details',
        title: 'Identification Number',
        value: '001214-14-0923'
      },
      {
        col: 4,
        icon: 'mdi-calendar-blank',
        title: 'Date of Birth',
        value: '14 Dec 2000'
      },
      {
        col: 4,
        icon: 'mdi-account',
        title: 'Age',
        value: '23'
      },
      {
        col: 4,
        icon: 'mdi-gender-male-female',
        title: 'Gender',
        value: 'Male'
      },
      {
        col: 4,
        icon: 'mdi-account-group',
        title: 'Race',
        value: this.issue.customer.race ?? 'Chinese'
      },
      {
        col: 4,
        icon: 'mdi-account-multiple',
        title: 'Marital Status',
        value: 'Single'
      }
    ]
  },
  methods: {
    getInitials(name) {
      return name
          .split(" ")           // Split by space (to handle multiple words)
          .map(word => word.slice(0, 1).toUpperCase())
          .slice(0, 2)
          .join("");            // Join them back together
    }
  }
}
</script>

<style scoped>
.blurry {
  position: absolute;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(25px);
}

.profile-pic {
  border: solid 4px white;
  background-color: lightcoral;
  color: white;

  span {
    font-size: 48px;
  }
}
</style>
