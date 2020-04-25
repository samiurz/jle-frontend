<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex xs12 md8>
        <material-card color="green" title="Create Profile" text="Complete your profile">
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex xs12 md4>
                  <v-select :items="branchs" label="Branch"></v-select>
                </v-flex>
                <v-flex xs12 md4>
                  <v-text-field label="Full Name" class="purple-input" />
                </v-flex>
                <v-flex xs12 md4>
                  <v-text-field class="purple-input" label="User Name" />
                </v-flex>
                <v-flex xs12 md4>
                  <v-select :items="roles" label="Role"></v-select>
                </v-flex>
                <v-flex xs12 md4>
                  <v-text-field label="Email Address" class="purple-input" />
                </v-flex>
                <v-flex xs12 md4>
                  <v-text-field label="Password" type="password" class="purple-input" />
                </v-flex>
                <v-flex xs12 md12>
                  <v-file-input accept="image/*" label="File input"></v-file-input>
                </v-flex>
                <v-flex xs12 text-xs-right>
                  <v-btn class="mx-0 font-weight-light" color="success">Update Profile</v-btn>
                </v-flex>
              </v-layout>
            </v-container>
          </v-form>
        </material-card>
      </v-flex>
      <v-flex xs12 md4>
        <material-card class="v-card-profile">
          <v-avatar slot="offset" class="mx-auto d-block" size="130">
            <img src="https://demos.creative-tim.com/vue-material-dashboard/img/marc.aba54d65.jpg" />
          </v-avatar>
          <v-card-text class="text-xs-center">
            <h6 class="category text-gray font-weight-thin mb-3">{{ user.function }}</h6>
            <h4 class="card-title font-weight-light">{{ fullname }}</h4>
            <p class="card-description font-weight-light">{{ user.description }}</p>
            <blockquote class="blockquote">{{ user.citation }}</blockquote>
            <v-btn color="success" round class="font-weight-light">Follow</v-btn>
          </v-card-text>
        </material-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import { mapGetters } from "vuex";
import materialCard from "~/components/material/AppCard";

export default {
  layout: "dashboard",
  components: {
    materialCard
  },


  data: () => ({
    branchs: ['Auckland & Upper Northland', 'Newplymouth', 'International'],
    roles: ['Admin','Manager','IT','Office Support','Contractor','Technician'],
  }),


  computed: {
    ...mapGetters({
      user: "user/getUser",
      fullname: "user/getFullname"
    })
  }
};
</script>
