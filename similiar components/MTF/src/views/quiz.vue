<template>
  <div>
    <div v-for="(quiz, index) in quizez" v-show="index === questionindex" :key="index">
      <h1>{{ quiz.category }}</h1>
      <el-card>
        <div>
          <h3 style="float:left;text-align:center;padding:40px">coloum A
            <ol>
              <li v-for="question in quiz.question" :key="question">
                <b>{{ question}}</b>
              </li>
            </ol>
          </h3>
          <h3 style="float:Right;text-align:center;padding:40px">colomn B
            <div class="fluid container">
              <div class="col-md-3">
                <draggable
                  class="list-group"
                  tag="ul"
                  v-model="list"
                  v-bind="dragOptions"
                  :move="onMove"
                  @start="isDragging=true"
                  @end="isDragging=false"
                >
                  <transition-group type="transition" :name="'flip-list'">
                    <li
                      class="list-group-item"
                      v-for="element in list"
                      :key="element.order"
                    >{{element.name}}</li>
                  </transition-group>
                </draggable>
              </div>
            </div>
          </h3>
        </div>
      </el-card>
    </div>
    <br>
    <br>
    <div v-if="questionindex < quizez.length">
      <center>
        <el-button type="danger" v-if="questionindex > 0" v-on:click="prev">prev</el-button>&emsp;
        <el-button type="success" v-on:click="next">next</el-button>
      </center>
    </div>
    <h1 v-if="questionindex == quizez.length">Your Total score is {{score}} / {{quizez.length}}</h1>
  </div>
</template>

<script>
import draggable from "vuedraggable";

var quiz_questions = [
  {
    category: "Maths",
    type: "mtf",
    difficulty: "easy",
    question: ["2*3", "1*0", "5*1"],
    correct_answers: ["6", "0", "5"]
  }
];
const message = quiz_questions[0].correct_answers;
export default {
  name: "app",
  components: {
    draggable
  },
  data() {
    return {
      list: message.map((name, index) => {
        return { name, order: index + 1 };
      }),
      editable: true,
      isDragging: false,
      delayedDragging: false,
      questionindex: 0,
      quizez: quiz_questions,
      answers: Array(quiz_questions.length).fill("")
    };
  },
  methods: {
    next: function() {
      this.questionindex++;
    },
    prev: function() {
      this.questionindex--;
    },
    onMove({ relatedContext, draggedContext }) {
      const relatedElement = relatedContext.element;
      const draggedElement = draggedContext.element;
      return (
        (!relatedElement || !relatedElement.fixed) && !draggedElement.fixed
      );
    }
  },
  computed: {
    dragOptions() {
      return {
        animation: 0,
        group: "description",
        disabled: !this.editable,
        ghostClass: "ghost"
      };
    },
    listString() {
      return JSON.stringify(this.list, null, 2);
    },
    score: function() {
      var total = 0;
      for (var i = 0; i < this.answers.length; i++) {
        if (this.answers[i] === this.quizez[i].correct_answer) {
          total += 1;
        }
      }
      return total;
    },
    randomList: function() {
      return quiz_questions[0].correct_answers.sort(function() {
        return 0.5 - Math.random();
      });
    }
  },
  watch: {
    isDragging(newValue) {
      if (newValue) {
        this.delayedDragging = true;
        return;
      }
      this.$nextTick(() => {
        this.delayedDragging = false;
      });
    }
  },
  mounted() {
    const obj = JSON.parse(window.localStorage.getItem("arr"));
    for (var i = 0; i < obj.length; i++) {
      this.quizez.push(obj[i]);
    }
  }
};
</script>

<style>
.flip-list-move {
  transition: transform 0.5s;
}

.no-move {
  transition: transform 0s;
}

.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.list-group {
  min-height: 20px;
}

.list-group-item {
  cursor: move;
}

.list-group-item i {
  cursor: pointer;
}
</style>
