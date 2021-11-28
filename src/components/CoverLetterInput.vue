<template>
  <div class="row justify-center q-col-gutter-md">
    <div class="col">
      <q-card class="card cover" bordered flat>
        <q-card-section>
          <div class="text-h6">Paste Cover Letter</div>
        </q-card-section>
        <q-card-section>
          <q-input
            v-model="coverInput"
            filled
            label="Cover Letter"
            hint="Paste cover letter here"
            type="textarea"
            @change="updateInput"
          />
        </q-card-section>
      </q-card>
    </div>
    <div class="col">
      <q-card class="card variable text-black" bordered flat>
        <q-card-section>
          <div class="text-h6">CL - Variables</div>
        </q-card-section>
        <q-card-section>
          <div
            class="item-input"
            v-for="(variable, index) in variableInput"
            :key="index"
          >
            <q-input
              class="q-mb-sm"
              filled
              :label="getInputLabel(variableInput[index])"
              v-model="variable[getInputLabel(variableInput[index])]"
            />
          </div>
        </q-card-section>
        <q-card-actions>
          <q-btn label="Update Cover Letter" @click="updateCoverLetter" />
        </q-card-actions>
      </q-card>
    </div>
  </div>
  <div class="q-mt-md">
    <div class="row justify-center">
      <q-card class="card">
        <q-card-section>
          <div class="text-h6">Updated Cover Letter</div>
        </q-card-section>
        <q-input readonly filled type="textarea" v-model="convertedInput" />
      </q-card>
    </div>
  </div>
</template>

<script>
import { ref, reactive } from "vue";
export default {
  name: "CoverLetterInput",
  setup() {
    const coverInput = ref("");
    const convertedInput = ref("");
    const variableInput = reactive([]);

    function getInputLabel(inputObject) {
      return Object.keys(inputObject)[0];
    }
    function flushInputs() {
      variableInput.splice(0);
      convertedInput.value = "";
    }
    function updateInput() {
      flushInputs();
      let inputName = "";
      let openBracket = false;
      let inputIndex = 0;
      let uniqueRef = [];
      // TODO @sam: Cleanup code by porting to regex capture
      for (var i = 0; i < coverInput.value.length; i++) {
        const currentLetter = coverInput.value[i];

        // -- Bracket Value Logic --
        if (currentLetter === "[") {
          openBracket = true;
          continue;
        } else if (currentLetter === "]") {
          openBracket = false;
        }

        // -- Object Data Binding Logic --
        if (openBracket) {
          inputName += currentLetter;
        } else if (inputName) {
          const newInput = { [inputName]: "" };
          if (!uniqueRef.includes(inputName)) {
            variableInput[inputIndex] = newInput;
            uniqueRef.push(inputName);
            inputName = "";
            inputIndex += 1;
          }
        }
      }
    }

    function updateCoverLetter() {
      convertedInput.value = coverInput.value;
      for (var i = 0; i < variableInput.length; i++) {
        const input = variableInput[i];
        for (var key in input) {
          if (input.hasOwnProperty(key)) {
            convertedInput.value = convertedInput.value.replaceAll(
              `[${key}]`,
              input[key]
            );
          }
        }
      }
    }
    return {
      variableInput,
      updateInput,
      coverInput,
      convertedInput,
      // Helpers
      getInputLabel,
      updateCoverLetter,
    };
  },
};
</script>

<style lang="scss" scoped>
.card {
  padding: 20px;
}
</style>
