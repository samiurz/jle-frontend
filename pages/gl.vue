<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex xs12 md8>
        <material-card color="green" title="Create General Ledger Code" text="">
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex xs12 md12>
                  <v-text-field label="GL Code" class="purple-input" />
                </v-flex>
                <v-flex xs12 text-xs-right>
                  <v-btn class="mx-0 font-weight-light" color="success">Update GL</v-btn>
              </v-flex>
              </v-layout>
            </v-container>
          </v-form>
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
