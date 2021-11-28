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
        <q-card-actions>
          <q-btn label="Clear" @click="clearCover">
            <q-tooltip class="bg-blue" :offset="[10, 10]">
              Clear cover letter input
            </q-tooltip>
          </q-btn>
        </q-card-actions>
      </q-card>
    </div>
    <div class="col">
      <q-card class="card variable text-black" bordered flat>
        <q-card-section>
          <div class="text-h6">CL - Variables</div>
        </q-card-section>
        <q-card-section v-if="variableInput.length > 0">
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
        <q-card-section v-else>
          <q-input label="No data" disable filled />
        </q-card-section>
        <q-card-actions>
          <q-btn label="Update Cover Letter" @click="updateCoverLetter">
            <q-tooltip class="bg-blue" :offset="[10, 10]">
              Apply variable changes
            </q-tooltip>
          </q-btn>
        </q-card-actions>
      </q-card>
    </div>
  </div>
  <div class="row justify-center q-mt-md">
    <div class="col">
      <q-card class="card">
        <q-card-section class="row justify-center">
          <div class="text-h6">Updated Cover Letter</div>
          <q-icon
            class="q-ml-sm"
            name="content_copy"
            size="2rem"
            @click="copy"
            v-if="convertedInput"
          >
            <q-tooltip class="bg-blue" :offset="[10, 10]">
              Click to Copy
            </q-tooltip></q-icon
          >
        </q-card-section>
        <q-input
          readonly
          filled
          type="textarea"
          v-model="convertedInput"
          :disable="!convertedInput"
          :label="convertedInput ? 'Updated Version' : 'No Data'"
        >
        </q-input>
      </q-card>
    </div>
  </div>
</template>

<script>
import { ref, reactive } from "vue";
import { useQuasar } from "quasar";
export default {
  name: "CoverLetterInput",
  setup() {
    const $q = useQuasar();

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

    function clearCover() {
      coverInput.value = "";
    }

    function copy() {
      if (convertedInput) {
        navigator.clipboard.writeText(convertedInput.value);
        $q.notify({
          message: "Copied",
          color: "green",
        });
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
      clearCover,
      copy,
    };
  },
};
</script>

<style lang="scss" scoped>
.card {
  padding: 20px;
}
</style>
