<template>
  <section id="main">
    <div class="text-center mt-2">
      <b-button
      v-if="$store.state.isLogged"
      variant="outline-primary"
      v-b-toggle.formquestions
      >ASK!</b-button>
    </div>
    <b-collapse id="formquestions" class="mt-2">
      <section id="ask-question">
        <p>ASKING IS ALWAYS FREE!</p>
        <b-form-input v-model="title" placeholder="Title..."
        class="mb-2"
        ></b-form-input>
        <wysiwyg v-model="myHTML"/>
        <b-form-tags v-model="tags" class="mt-2"></b-form-tags>
        <div class="text-right mt-2">
          <b-button @click="publishQuestion"
          variant="outline-primary">Publish Question</b-button>
        </div>
      </section>
    </b-collapse>
    <section id="questions" class="mt-4">
      <div id="question">
        <Card
        v-for="(question, i) in $store.state.questions"
        :key="i"
        :question="question"
        />
      </div>
    </section>
  </section>
</template>

<script>
import axios from '../config/server';
import Card from '@/components/card.vue';

export default {
  name: 'questions',
  components: { Card },
  data() {
    return {
      title: '',
      tags: [],
      myHTML: '',
    };
  },
  methods: {
    async publishQuestion() {
      try {
        const payload = {
          title: this.title,
          description: this.myHTML,
          tags: this.tags,
        };
        await axios.post('/questions', payload, { headers: { token: localStorage.getItem('token') } });
        this.$store.dispatch('fetchData');
        this.$store.dispatch('getMyQuestions');
        this.$root.$emit('bv::toggle::collapse', 'formquestions');
        this.title = '';
        this.description = '';
        this.tags = '';
      } catch (err) {
        // send swall
        if (!err.response.data.errors.join(',')) {
          this.$swal('Opps... something when wrong');
        } else {
          this.$swal(err.response.data.errors.join(','));
        }
      }
    },
  },
  created() {
    this.$store.dispatch('fetchData');
  },
};
</script>

<style scoped>
@import "~vue-wysiwyg/dist/vueWysiwyg.css";
#ask-question {
  width: 50vw;
  transform: translateX(50%);
  left: 50%;
}
p {
  font-size: 1.5em;
}
#question {
  width: 50vw;
  transform: translateX(50%);
  left: 50%;
}
</style>
