<template>
  <d-card class="card-small user-details mb-4">

    <!-- Card Header -->
    <d-card-header class="p-0">
      <div class="user-details__bg">
        <img :src="coverImg" :alt="user.fullname">
      </div>
    </d-card-header>

    <d-card-body class="p-0">

      <!-- User Avatar -->
      <div class="user-details__avatar mx-auto">
        <img :src="avatarImg" :alt="user.fullname">
      </div>

      <!-- User Name -->
      <h4 class="text-center m-0 mt-2">{{ user.fullname }}</h4>

      <!-- User Social -->
      <ul class="user-details__social user-details__social--primary d-table mx-auto mb-4">
        <li v-if="social.facebook" class="mx-1"><a :href="social.facebook"><i class="fab fa-facebook-f"></i></a></li>
        <li v-if="social.twitter" class="mx-1"><a :href="social.twitter"><i class="fab fa-twitter"></i></a></li>
        <li v-if="social.github" class="mx-1"><a :href="social.github"><i class="fab fa-github"></i></a></li>
        <li v-if="social.slack" class="mx-1"><a :href="social.slack"><i class="fab fa-slack"></i></a></li>
      </ul>

      <!-- User Data -->
      <div class="user-details__user-data border-top border-bottom p-4">
        <div class="row mb-3">
          <div class="col w-50">
            <span>Email</span>
            <span>{{ user.email }}</span>
          </div>
          <div class="col w-50">
            <span>Location</span>
            <span>Jakarta</span>
          </div>
        </div>
        <div class="row">
          <div class="col w-50">
            <span>Phone</span>
            <span>089982934575</span>
          </div>
          <div class="col w-50">
            <span>Account Number</span>
            <span>1234</span>
          </div>
        </div>
      </div>

      <!-- User Tags -->
      <!-- <div class="user-details__tags p-4">
        <d-link v-for="(tag, idx) in tags" :key="idx" :to="tag.url + user._id">
          <span class="badge badge-pill badge-light text-light text-uppercase mb-2 border" style="cursor: pointer;">{{ tag.name }}</span>
        </d-link>
      </div> -->

    </d-card-body>
  </d-card>
</template>

<script>
import gql from '@/gql';
import graphqlFunction from '@/graphqlFunction';
import basicFunction from '@/basicFunction';
import address from '@/address';
import headers from '@/headers';
export default {
  name: 'user-details',
  data(){
    return {
      user: {},
      coverImg: require('@/assets/images/user-profile/up-user-details-background.jpg'),
      avatarImg: "",
      social: {
        facebook: '#',
        twitter: '#',
        github: '#',
        slack: '#',
      },
      tags: [
        {
          'name': 'Arus Kas',
          'url': '/rekap-arus-kas?id='
        },
        {
          'name': 'Laba Rugi',
          'url': '/rekap-laba-rugi?id='
        },
        {
          'name': 'Neraca Keuangan',
          'url': '/rekap-neraca?id='
        },
        {
          'name': 'Penerimaan Negara',
          'url': '/rekap-penerimaan-negara?id='
        }
      ],
    }
  },

  created: function()
  {
      this.fetchUser();
  },

  methods: {
    fetchUser() {
      var id = window.location.href.split("user-profile/")[1];
      this.axios.get(address + ":3000/get-user", headers).then((response) => {
        let query = gql.singleUser;
        let variable = {
          userId: id
        };
        graphqlFunction.graphqlFetchOne(query, variable, (result) => {
          this.user = result.user;
          this.avatarImg = require('@/assets/images/uploads/' + this.user.profile_picture + '.png');
          for(var i = 0; i < this.tags.length; i++) {
            if(this.user.role == "user") {
              if(this.tags[i].name == "HPP") {
                if(this.user.tahapan_kegiatan == "Eksplorasi") {
                  this.tags.splice(i, 1);
                }
              }
            }
            else {
              this.tags = [];
              break;
            }
          }
        });
      })
    },
  }
};
</script>
