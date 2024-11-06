<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" md="6">
        <h1 class="text-center">Enter Statistics</h1>

        <v-form ref="formRef" @submit.prevent="submitData">
          <!-- State Dropdown -->
          <v-select
            v-model="form.state"
            :items="filteredStates"
            label="Choose State"
            :rules="[v => !!v || 'State is required']"
            required
          ></v-select>

          <!-- Date Picker -->
          <div class="town mx-3">
      <h6>Date</h6>
      <input 
        type="date" 
        class="form-control date-formate" 
        aria-label="Large" 
        aria-describedby="inputGroup-sizing-sm"
        v-model="form.date"
      />
      </div>
    <!-- <div>
      <datepicker v-model="date" lang="fr" locale="date-fns/locale/fr" 
 format="dd-MM-YYYY" type="date" :lowerLimit="new Date()"></datepicker>
                                        
    </div> -->
          <!-- Numeric Fields for Cases, Recovery, Deaths -->
          <v-text-field
            v-model.number="form.newCases"
            label="No. of new cases Today"
            type="number"
            min="0"
            :rules="[v => !!v || 'Number of cases is required']"
            required
          ></v-text-field>

          <v-text-field
            v-model.number="form.recovery"
            label="No. of Recovery Today"
            type="number"
            min="0"
            :rules="[v => !!v || 'Number of recoveries is required']"
            required
          ></v-text-field>

          <v-text-field
            v-model.number="form.deaths"
            label="No. of Deaths Today"
            type="number"
            min="0"
            :rules="[v => !!v || 'Number of deaths is required']"
            required
          ></v-text-field>

          <!-- Submit Button -->
          <v-btn color="primary" class="mt-4" @click="submitData">Update</v-btn>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { ref, reactive, computed } from "vue";

export default {
  setup() {
    const date = ref(new Date());
    const states = ref([
      { name: "Assam", cases: 4357 },
      { name: "Delhi", cases: 8934 },
      { name: "Gujarat", cases: 3894 },
      { name: "Madhya Pradesh", cases: 4572 },
      { name: "Maharashtra", cases: 6584 },
      { name: "Rajasthan", cases: 10830 },
      { name: "Tamil Nadu", cases: 7516 },
      { name: "Uttar Pradesh", cases: 4320 },
    ]);

    const form = reactive({
      state: "",
      date: null,
      newCases: null,
      recovery: null,
      deaths: null,
    });

    const inputData = ref([]);
    const menu = ref(false); // Controls the date picker menu

    // Table headers for displaying statistics
    const headers = [
      "Total Cases Reported",
      "Recovered",
      "Deaths"
    ];

    // Filtered and sorted list of states for dropdown
    const filteredStates = computed(() =>
      states.value
        .filter((state) => state.cases > 0)
        .sort((a, b) => b.cases - a.cases)
        .map((state) => state.name)
    );

    const onDateSelected = (value) => {
      form.date = value;
      menu.value = false; // Close the date picker menu after selecting a date
    };

    const submitData = () => {
      if (
        form.state &&
        form.date &&
        form.newCases !== null &&
        form.recovery !== null &&
        form.deaths !== null
      ) {
        // Check if the entry for the date and state already exists
        const existingEntry = inputData.value.find(
          (entry) => entry.date === form.date && entry.state === form.state
        );
        
        if (existingEntry) {
          existingEntry.newCases = form.newCases;
          existingEntry.recovery = form.recovery;
          existingEntry.deaths = form.deaths;
        } else {
          // Add new entry if it doesn't exist
          inputData.value.push({ ...form });
        }
        console.log('inputs:', inputData)
      }
    };


    return {
      form,
      menu,
      date,
      filteredStates,
      onDateSelected,
      submitData,
      headers,
    };
  },
};
</script>

<style scoped>
.text-center {
  text-align: center;
}
</style>
