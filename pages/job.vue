<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex xs12 md12>
        <material-card color="green" title="Job" text="Job profile">
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex xs12 md6>
                  <v-select :items="customers" label="Select Customer"></v-select>
                </v-flex>
                <v-flex xs12 md6>
                  <v-select :items="priorities" label="Select Job Priority"></v-select>
                </v-flex>
                <v-flex xs12 md6>
                  <v-text-field label="Job Code" class="purple-input" />
                </v-flex>
                <v-flex xs12 md6>
                  <v-select
                    v-model="e7"
                    :items="employees"
                    label="Select Employee"
                    multiple
                    chips
                    hint="Assign Job"
                    persistent-hint
                  ></v-select>
                </v-flex>
                <v-flex xs12 md12>
                  <v-textarea
                    outlined
                    name="input-7-4"
                    label="Job Description"
                    value="The Woodman set to work at once, and so sharp was his axe that the tree was soon chopped nearly through."
                  ></v-textarea>
                </v-flex>
                <v-flex xs12 md12>
                  <v-file-input multiple label="File input"></v-file-input>
                </v-flex>
                <v-flex xs12 text-xs-right>
                  <v-btn class="mx-0 font-weight-light" color="success">Update Job</v-btn>
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

  data () {
    return {
      e6: [],
      e7: [],
      employees: ['Lortia Teao', 'Chantal Chutte', 'Duncan Sheils', 'Shafeed Khan'],
      customers: ["Nestle Professionals", "Holcim", "PNG"],
      priorities: ["Low", "Moderate", "High", "Urgent"],
      employees: [
        "Lorita Teao",
        "Chantal Chutte",
        "Duncan Sheils",
        "Shafeed Khan"
      ],
    }
  },

  computed: {
    ...mapGetters({
      user: "user/getUser",
      fullname: "user/getFullname"
    })
  }
};
</script>
