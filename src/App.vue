
<template>
  <v-toolbar :elevation="8" title="Quiz Application"></v-toolbar>


  <v-card class="mx-auto" max-width="800">
    <v-alert type="info">{{ question.QuestionStem }}</v-alert>
    <v-list lines="one">
      <v-list-item v-for="item in items" :key="item.id" :title="item.title" @click="checkAnswer(item.id)"></v-list-item>
    </v-list>
    <v-btn variant="outlined" @click="refreshPage">
      Next
    </v-btn>
  </v-card>

  <v-dialog v-model="dialog" width="auto">
    <v-card>
      <v-card-text>
        {{ question.AdditionalInformation }}
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" block @click="dialog = false">关闭附加信息</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-row justify="center">
    <v-dialog v-model="dialog_1" persistent width="auto">
      <v-card>
        <v-card-title class="text-h5">
          回答正确，正确答案是 {{ question.Answer }}
        </v-card-title>
        <v-card-text>是否立即跳转到下一题？</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green-darken-1" variant="text" @click="dialog_1 = false">
            否
          </v-btn>
          <v-btn color="green-darken-1" variant="text" @click="refreshPage">
            下一题
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue'
import axios from 'axios';

interface Question {
  AdditionalInformation: string
  QuestionStem: string
  Answer: string
  A: string
  B: string
  C: string
  D: string
  E: string
  F: string
  G: string
}
type Employee = {
  id: string;
  title: string;
};
export default defineComponent({
  data() {
    return {
      question: {} as Question,
      error: '',
      items: new Array<Employee>(),
      dialog: false,
      dialog_1: false,
    };
  },
  created() {
    this.fetchQuestion();
  },
  methods: {
    async fetchQuestion() {
      try {
        const response = await axios.get('/api/next_question');
        this.question = response.data as Question;
        if (response.data["A"] != "") this.items.push({ id: "A", title: response.data["A"] } as Employee);
        if (response.data["B"] != "") this.items.push({ id: "B", title: response.data["B"] } as Employee);
        if (response.data["C"] != "") this.items.push({ id: "C", title: response.data["C"] } as Employee);
        if (response.data["D"] != "") this.items.push({ id: "D", title: response.data["D"] } as Employee);
        if (response.data["E"] != "") this.items.push({ id: "E", title: response.data["E"] } as Employee);
        if (response.data["F"] != "") this.items.push({ id: "F", title: response.data["F"] } as Employee);
        if (response.data["G"] != "") this.items.push({ id: "G", title: response.data["G"] } as Employee);
      } catch (error) {
        this.error = 'Failed to fetch question.';
      }
    },
    refreshPage() {
      location.reload();
    }, checkAnswer(id: String) {
      const selectedAnswer = id
      if (selectedAnswer === this.question.Answer) {
        this.dialog_1 = true;
      } else {
        this.dialog = true;
      }
    }
  },
});
</script>