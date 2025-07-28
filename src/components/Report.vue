<template>
  <div class="container1">
    <div class="question-header">
      <h2>{{ `Question ${questionIndex + 1}:` }}</h2>
      <div class="timer">{{ `Time: ${minutes}:${seconds}` }}</div>
    </div>
    <div class="container2">
      <p class="question-title" v-html="question.title"></p>
<!--      <p class="question-references" v-html="question.references" style="display:block;"></p>-->
<!--      <p>References:</p>-->
  <div class="question-detail" v-html="question.detail" style="display:block; white-space:pre-wrap">
  </div>
      <p class="question-note">Note: Identify all configurations for the coding standard. A coding standard may have <b>none, one, or multiple</b> corresponding configurations. If you believe there are no matched configurations, write "<b>None</b>". <b style="color: red; font-size: 1rem;" >The 'Configuration Reference' section provides configurations for your reference.</b></p>
<!--<p>References:</p>-->

  <code class="question-references" v-html="question.references" style="display:block; white-space:pre-wrap">
  </code>

      <div v-for="index in tupleIndex" >
        <ReportTuple :question="question" :able-edit="ableEdit" :key="index" :name="index" :ref="'reportTuples_'+index"/>
        <hr>
      </div>
<!--      <button @click="addQuestion" :disabled="!ableEdit">+</button>-->
    </div>
    <div class="container3">
      <button @click="nextQuestion" class="next-button">Next</button>
    </div>
  </div>
</template>

<script>
import ReportTuple from './ReportTuple.vue'
import { saveAs } from 'file-saver';

export default {
  name: "Report",
  components: {
    ReportTuple
  },
  data() {
    return {
      questionIndex: 0,
      minutes: 10,
      seconds: 0,
      tupleIndex: 1,
      ableEdit: true,
      answers: [],
      finalStoredData:{},
      questions: [
      {
title: "For the following Java Coding Standard, generate configurations based on the following Linter Configurations",
detail:"<b> Java Coding Standard:</b>\n \
  No static import for classes:\n \
  Static import is not used for static nested classes. They are imported with normal imports.\n \
\n \
<b>Linter Configurations:</b><p><a href=\"https://checkstyle.sourceforge.io/checks.html\"> Right Click the link and then Click 'Open Link in New Tab' to See All Linter rules!</a></p>",
references:"<b>Configuration Reference:</b>\n \
None"
        },
           {
title: "For the following Java Coding Standard, generate configurations based on the following Linter Configurations",
detail:"<b> Java Coding Standard:</b>\n \
Use of optional braces:\n \
Braces are used with `if` , `else` , `for` , `do` and `while` statements, even when the body is empty or contains only a single statement. \n \
Other optional braces, such as those in a lambda expression, remain optional. \n \
\n \
<b>Linter Configurations:</b><p><a href=\"https://checkstyle.sourceforge.io/checks.html\"> Right Click the link and then Click 'Open Link in New Tab' to See All Linter rules!</a></p>",
references:"<b>Configuration Reference:</b>\n \
&ltmodule name=\"NeedBraces\"&gt\n \
&ltproperty name=\"allowEmptyLoopBody\" value=\"false\"/&gt\n \
&ltproperty name=\"allowSingleLineStatement\" value=\"false\"/&gt\n \
&ltproperty name=\"tokens\" value=\"LITERAL_DO, LITERAL_ELSE, LITERAL_FOR, LITERAL_IF, LITERAL_WHILE\"/&gt\n \
&lt/module&gt"
        },
          {
title: "For the following Java Coding Standard, generate configurations based on the following Linter Configurations",
detail:"<b> Java Coding Standard:</b>\n \
Block tags:\n \
Any of the standard \"block tags\" that are used appear in the order `@param` , `@return` , `@throws` , `@deprecated` , and these four types never appear with an empty description. When a block tag doesn't fit on a single line, continuation lines are indented four (or more) spaces from the position of the `@` .\
\n \
<b>Linter Configurations:</b><p><a href=\"https://checkstyle.sourceforge.io/checks.html\"> Right Click the link and then Click 'Open Link in New Tab' to See All Linter rules!</a></p>",
references:"<b>Configuration Reference:</b>\n \
&ltmodule name=\"AtclauseOrder\"&gt\n \
  &ltproperty name=\"id\" value=\"atclause_order\"/&gt\n \
  &ltproperty name=\"tagOrder\" value=\"@param, @return, @throws, @deprecated\"/&gt\n \
&lt/module&gt\n \
&ltmodule name=\"NonEmptyAtclauseDescription\"&gt\n \
  &ltproperty name=\"javadocTokens\" value=\"PARAM_LITERAL, RETURN_LITERAL, THROWS_LITERAL, DEPRECATED_LITERAL\"/&gt\n \
&lt/module&gt\n \
&ltmodule name=\"JavadocTagContinuationIndentation\"&gt\n \
  &ltproperty name=\"offset\" value=\"4\"/&gt\n \
&lt/module&gt"
        },
          {
title: "For the following Java Coding Standard, generate configurations based on the following Linter Configurations",
detail:"<b> Java Coding Standard:</b>\n \
Vertical Whitespace:\n \
A single blank line always appears:\n \
Between consecutive members or initializers of a class: fields, constructors, methods, nested classes, static initializers, and instance initializers.\n \
Exception: A blank line between two consecutive fields (having no other code between them) is optional. Such blank lines are used as needed to create logical groupings of fields.\n \
A single blank line may also appear anywhere it improves readability, for example between statements to organize the code into logical subsections. A blank line before the first member or initializer, or after the last member or initializer of the class, is neither encouraged nor discouraged.\n \
Multiple consecutive blank lines are permitted, but never required (or encouraged).\n \
\n \
<b>Linter Configurations:</b><p><a href=\"https://checkstyle.sourceforge.io/checks.html\"> Right Click the link and then Click 'Open Link in New Tab' to See All Linter rules!</a></p>",
references:"<b>Configuration Reference:</b>\n \
&ltmodule name=\"EmptyLineSeparator\"&gt\n \
  &ltproperty name=\"allowMultipleEmptyLines\" value=\"true\"/&gt\n \
  &ltproperty name=\"allowNoEmptyLineBetweenFields\" value=\"true\"/&gt\n \
  &ltproperty name=\"tokens\" value=\"VARIABLE_DEF, CTOR_DEF, METHOD_DEF, CLASS_DEF, STATIC_INIT, INSTANCE_INIT\"/&gt\n \
  &lt/module&gt"
        },
          {
title: "For the following Java Coding Standard, generate configurations based on the following Linter Configurations",
detail:"<b>Java Coding Standard:</b>\n \
  Empty blocks: may be concise:\n \
  An empty block or block-like construct may be in K & R style. Alternatively, it may be closed immediately after it is opened, with no characters or line break in between ( `{}` ), unless it is part of a multi-block statement (one that directly contains multiple blocks: `if/else` or `try/catch/finally` ).\n \
Examples:\n \
  // This is acceptable\n \
  void doNothing() {}\n \
\
  // This is equally acceptable\n \
  void doNothingElse() {\n \
  }\n \
\n \
<b>Linter Configurations:</b><p><a href=\"https://checkstyle.sourceforge.io/checks.html\"> Right Click the link and then Click 'Open Link in New Tab' to See All Linter rules!</a></p>",
references:"<b>Configuration Reference:</b>\n \
None"
        },
{
title: "For the following Java Coding Standard, generate configurations based on the following Linter Configurations",
detail:"<b>Java Coding Standard:</b>\n \
Block indentation: +2 spaces:\n \
Each time a new block or block-like construct is opened, the indent increases by two spaces. When the block ends, the indent returns to the previous indent level. The indent level applies to both code and comments throughout the block. \n \
\n \
<b>Linter Configurations:</b><p><a href=\"https://checkstyle.sourceforge.io/checks.html\"> Right Click the link and then Click 'Open Link in New Tab' to See All Linter rules!</a></p>",
references:"<b>Configuration Reference:</b>\n \
&ltmodule name=\"Indentation\"&gt\n \
  &ltproperty name=\"basicOffset\" value=\"2\"/&gt\n \
&lt/module&gt\n \
&ltmodule name=\"CommentsIndentation\"&gt\n \
  &ltproperty name=\"tokens\" value=\"SINGLE_LINE_COMMENT, BLOCK_COMMENT_BEGIN\"/&gt\n \
&lt/module&gt"
        }
      ],
    }
  },
  created() {
    this.createAnswer(this.tupleIndex);
  },
  computed: {
    question() {
      return this.questions[this.questionIndex]
    },
  },
  methods: {
    startTimer() {
      setInterval(() => {
        if (this.seconds > 0) {
          this.seconds--
        } else if (this.minutes > 0) {
          this.minutes--
          this.seconds = 59
        } else {
          // Time is up
          this.ableEdit = false
        }
      }, 1000)
    },
    addQuestion() {
      // Code to add a new question
      this.tupleIndex += 1;
      this.createAnswer(this.tupleIndex);
    },
    createAnswer(index) {
      var answer = {'tuple_id':index};
      this.answers.push(answer);
    },
    resetParameters() {
      this.minutes = 10;
      this.seconds = 0;
      this.tupleIndex = 1;
      this.ableEdit = true;
      this.answers = [];
      this.$refs['reportTuples_1'][0].resetParameters();
      this.createAnswer(this.tupleIndex);
    },
    nextQuestion(){
      console.log(this.$refs)
      for (var i=0;i<this.tupleIndex;i++) {
        this.answers[i]['selectedOption'] = this.$refs['reportTuples_'+(i+1)][0].selectedOption
        this.answers[i]['reason'] = this.$refs['reportTuples_'+(i+1)][0].reason
        this.answers[i]['references'] = this.$refs['reportTuples_'+(i+1)][0].references
      }
      console.log(this.answers)
      this.finalStoredData['answers_'+(this.questionIndex+1)] = {'answers':this.answers, 'min':9-this.minutes,
        'sec':60-this.seconds}

      if (this.questionIndex < 5) {
        this.questionIndex += 1;
        this.resetParameters()
      } else {
        this.saveAsJSON();
        this.$router.push('/thanks');
      }
    },
    saveAsJSON() {
      const data = JSON.stringify(this.finalStoredData, null, 2);
      const filename = this.$route.params.name + '.json';
      const blob = new Blob([data], { type: 'text/plain;charset=utf-8' });
      saveAs(blob, filename);
    }

  },
  mounted() {
    this.startTimer();
  }
}
</script>

<style scoped>
.container1 {
  width: 66.67%;
  margin: auto;
}
.container2 {
  width: 90%;
  margin: auto;
  text-align: left;
  font-size: 1.2rem;
}
.question-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.question-title {
  margin-bottom: 1.5rem;
}
.question-detail{
font-size: 0.8rem;
width: 100%;
}
.question-references{
font-size: 0.8rem;
width: 100%;
}
.timer {
  font-size: 1.2rem;
}
.container3 {
  display: flex;
  width: 100%;
  margin-top: 1.5rem;
}
.next-button {
  margin-left: auto;
}
.question-note{
font-size: 0.8rem;
width: 100%;
}
.code {
  display: block;
  white-space: pre-wrap;
  font-size: 0.4rem;
}
</style>