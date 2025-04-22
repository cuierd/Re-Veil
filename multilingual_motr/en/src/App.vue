<!-- Window is fixed, 102px, pointer cursor, gradual blurry effect on surrounding words. -->
<!--  Comprehension questions appear afterwards in the same slide -->

<template>
  <Experiment title="Mouse tracking for Reading" translate="no">

    <Screen :title="'welcome'" class="instructions" :validations="{
        SubjectID: {
          minLength: $magpie.v.minLength(2)
        }
      }">
        <!-- <WelcomeScreen /> -->
        <div style="width: 40em; margin: auto;">

        <div style="background-color: lightgrey; padding: 10px;">
          <b> About this study </b>
        </div>
        <p>
          Welcome to this experiment. Please read the following information carefully. If you have any questions, feel free to contact us.
        <br><br>
          <b>What is the study about?</b> You will be participating in a study conducted by the Digital Linguistics Group at the University of Zurich. This research will help us understand how people read.
        <br><br>
          <b>What do I need to do?</b> You will use the computer mouse to read sentences in English and answer questions about them.
        <br><br>
          <b>What are my rights?</b> You may change your mind and leave the study at any time by closing the web page without specifying reasons and without any disadvantages.
        <br><br>
          <b>What are my rights regarding the data?</b> You can request information about the personal data collected from you at any time and without giving reasons. You can also request that it be rectified, handed over to you, barred for processing or erased. To do so, please contact the person indicated below.
        <br><br>
          <b>Contact:</b> Cui Ding [cui.ding@uzh.ch] Department of Computational Linguistics, Andreasstr. 15, CH-8050 Zurich, Switzerland <br>
        </p>

        <br>
        <div style="background-color: lightgrey; padding: 10px;">
          <b> Consent Form </b>
        </div>
        <br> I, as a participant, confirm by clicking the button below: <br>
          <div style="padding-left: 30px"> • I have read and understood the study information. </div>
          <div style="padding-left: 30px">• I comply with the inclusion and exclusion criteria for participation described above. I am aware of the requirements and restrictions to be observed during the study. </div>
          <div style="padding-left: 30px">• I have had enough time to decide about my participation. </div>
          <div style="padding-left: 30px">• I participate in this study voluntarily and consent that my personal data be used as described above.</div>
          <div style="padding-left: 30px">• I understand that I can stop participating at any moment.</div>
        <br>

        <tr>
          <td>Please enter your Prolific ID to continue:&nbsp</td><td><input name="TurkID" type="text" class="obligatory" v-model="$magpie.measurements.SubjectID"/></td>
        </tr>
        <tr></tr>

        </div>
          <div v-if="
            $magpie.measurements.SubjectID&&
            !$magpie.validateMeasurements.SubjectID.$invalid
            ">
          <br> By clicking on the button below you consent to participating in this study: <br><br>
          <br />
          <button 
            @click="$magpie.addExpData({ SubjectId: $magpie.measurements.SubjectID}); $magpie.nextScreen()">
            Proceed
          </button>

        </div>
     </Screen>



     <InstructionScreen :title="'Instructions'" :button-text="'Continue'">
      <p></p>
      <p>This study explores how much visual information is needed to read. If only the top or bottom half of letters is visible, can people still understand the text? And which half helps more?</p>
      <p>To investigate, you’ll read short passages that appear blurred. Move your mouse over the text to reveal it. Some texts will show only the top half, some the bottom, and some the full text.</p>
      <p>Do your best to read and understand each one. After reading, click “Answer Questions” to continue.</p>
      <p>Let’s get started!</p>
    </InstructionScreen>

    <template v-for="(trial, i) of trials">
      <Screen :key="i" class="main_screen" :progress="i / trials.length">
        <Slide class="text_slide">
          <form>
            <input type="hidden" class="item_id" :value="trial.item_id">
            <input type="hidden" class="stimulus_id" :value="trial.stimulus_id">
            <input type="hidden" class="page_id" :value="trial.page_id">
            <input type="hidden" class="question_id" :value="trial.question_id">
            <input type="hidden" class="wordRealPart" :value="trial.wordRevealPart">
          </form>
          <div class="oval-cursor"></div>
          <template>
            <div class="readingText" @mousemove="moveCursor" @mouseleave="changeBack">
              <div v-for="(para, paraIndex) of trial.text.split('@#@')">
                <template v-for="(word, index) of para.split(' ')">
                  <span v-if="trial.wordRevealPart==='l'" style="clip-path: inset(55% 0 0 0);" :key="index" :data-index="`${paraIndex}-${index}`">
                    {{ word }}
                  </span>
                  <span v-else-if="trial.wordRevealPart==='u'" style="clip-path: inset(0 0 40% 0);" :key="index" :data-index="`${paraIndex}-${index}`">
                    {{ word }}
                  </span>
                  <span v-else :key="index" :data-index="`${paraIndex}-${index}`">
                    {{ word }}
                  </span>
                </template>
              </div>
            </div>
            <div class="blurry-layer" style="opacity: 0.3; filter: blur(4.5px); transition: all 0.3s linear 0s;"> 
              <!-- {{trial.text}} -->
              <div v-for="(para, paraIndex) of trial.text.split('@#@')">
                <template v-for="(word, index) of para.split(' ')">
                  <span v-if="trial.wordRevealPart==='l'" style="clip-path: inset(55% 0 0 0);" :key="index" :data-index="`${paraIndex}-${index}`">
                    {{ word }}
                  </span>
                  <span v-else-if="trial.wordRevealPart==='u'" style="clip-path: inset(0 0 40% 0);" :key="index" :data-index="`${paraIndex}-${index}`">
                    {{ word }}
                  </span>
                  <span v-else :key="index" :data-index="`${paraIndex}-${index}`">
                    {{ word }}
                  </span>
                </template>
              </div>
            </div>
          </template>

          <button style= "bottom: 10%; transform: translate(-50%, -50%)" @click="trial.question_id == null ? saveAndDisable() : handleRCQButton()" :disabled="!hasRead">
                  {{ trial.question_id == null ? 'Next Page': 'Answer Question' }}
          </button>
        </Slide>

        <Slide class="question_slide" v-if="trial.question_id">
          <div class="radio-options">
            <form>
              <input type="hidden" class="question_id" :value="trial.question_id">
              <p>{{ trial.question}}</p>
                <template v-for='(option, index) of trial.response_options'>
                  <input :id="'opt_'+index" type="radio" :value="option" name="opt"  v-model="$magpie.measurements.response" />&nbsp&nbsp{{option}}<br/>
                </template>
            </form>
          </div>
          <!-- <button v-if="$magpie.measurements.response"  style="transform: translate(-50%, -50%)" @click="$magpie.saveMeasurements(); $magpie.measurements.response=''; j !== trial.length - 1 ? $magpie.nextSlide() : $magpie.nextScreen()"> -->
            <button v-if="$magpie.measurements.response" style="bottom: 10%; transform: translate(-50%, -50%)" @click="$magpie.saveAndNextScreen()">
            {{ 'Next Page' }}
          </button>
        </Slide>

      </Screen>
    </template>
<Screen>
  <p>1. Which do you find EASIER to read: text showing only the top half or only the bottom half?</p>
    <MultipleChoiceInput
      :response.sync="$magpie.measurements.difficulty"
      orientation="horizontal"
      :options="['Top half only', 'Bottom half only', 'About the same']" />
  <br>
  <br>
  <p>2. Which input device are you using for this experiment?</p>
    <MultipleChoiceInput
        :response.sync= "$magpie.measurements.device"
        orientation="horizontal"
        :options="['Mouse', 'Trackpad', 'Stylus', 'Other']" />
  <br>
  <br>
  <p>3. What operating system did you use during this experiment?</p>
    <MultipleChoiceInput
        :response.sync="$magpie.measurements.os"
        orientation="horizontal"
        :options="['Microsoft Windows', 'Apple macOS', 'Other']" />
  <button style= "bottom: 5%; transform: translate(-50%, -50%)" @click="$magpie.saveAndNextScreen();">Submit</button>
</Screen>

    <SubmitResultsScreen />
  </Experiment>
</template>

<script>

// Load data from csv files as javascript arrays with objects
// import onestop_zh_practice from '../trials/onestop_zh_practice.tsv';
import onestop_zh from '../trials/onestop_en.tsv';
import _ from 'lodash';

export default {
  name: 'App',
  data() {
    const groupedItems = _.groupBy(onestop_zh, 'stimulus_id');
    const shuffledGroups = _.shuffle(Object.values(groupedItems));
    const shuffledItems = _.flatMap(shuffledGroups);
    // const trials = _.concat(onestop_zh_practice, shuffledItems);
    const trials = shuffledItems;

    const wordRevealParts = ['f', 'u', 'l'] // which part of the word to reveal: full, upper half, lower half
    const shuffledWordRevealParts = _.shuffle(Array.from({ length: trials.length }, (_, i) => wordRevealParts[i % wordRevealParts.length]));
    // Ensure first trial gets 'f'
    if (shuffledWordRevealParts[0] !== 'f') {
      const fIndex = shuffledWordRevealParts.indexOf('f');
      if (fIndex !== -1) {
        // Swap the first item with the first occurrence of 'f'
        [shuffledWordRevealParts[0], shuffledWordRevealParts[fIndex]] = [shuffledWordRevealParts[fIndex], shuffledWordRevealParts[0]];
      }
    }
    const updatedTrials = trials.map((trial, index) => ({
      ...trial,
      response_options: _.shuffle([trial.response_true, trial.distractor_1, trial.distractor_2, trial.distractor_3]),
      wordRevealPart: shuffledWordRevealParts[index]
    }));
    
    // const wordRevealPartsAssigned = updatedTrials.map(trial => trial.wordRevealPart);
    // console.log('All assigned wordRevealParts:', wordRevealPartsAssigned);
    // const counts = _.countBy(wordRevealPartsAssigned);
    // console.log('Counts of each wordRevealPart:', counts);
    

    return {
      hasRead: false,
      // isCursorMoving: false,
      trials: updatedTrials,
      currentIndex: null,
      // currentItem: null,
      mousePosition: {
          x: 0,
          y: 0,
        },
  }},
  computed: {
  },
  mounted() { 
    setInterval(this.saveData, 50);
    },
  methods: {
    changeBack() {
      this.$el.querySelector(".oval-cursor").classList.remove('grow');
      this.$el.querySelector(".oval-cursor").classList.remove('blank');
      this.currentIndex = null;
    },
    saveData() {
        if (this.currentIndex !== null) {
          const currentElement = this.$el.querySelector(`span[data-index="${this.currentIndex}"]`);
          if (currentElement) {
            const currentElementRect = currentElement.getBoundingClientRect();
            $magpie.addTrialData({
              Stimulus: this.$el.querySelector(".stimulus_id").value,
              // Condition: this.$el.querySelector(".condition_id").value,
              ItemId: this.$el.querySelector(".item_id").value,
              PageId: this.$el.querySelector(".page_id").value,
              Index: this.currentIndex,
              Word: currentElement.innerHTML,
              mousePositionX: this.mousePosition.x,
              mousePositionY: this.mousePosition.y,
              // wordPositionTop: currentElementRect.top,
              // wordPositionLeft: currentElementRect.left,
              // wordPositionBottom: currentElementRect.bottom,
              // wordPositionRight: currentElementRect.right,
              wordRevealPart: this.$el.querySelector(".wordRealPart").value,
              // wordPositionTop: currentElement.offsetTop,
              // wordPositionLeft: currentElement.offsetLeft,
              // wordPositionBottom: currentElement.offsetHeight + currentElement.offsetTop,
              // wordPositionRight: currentElement.offsetWidth + currentElement.offsetLeft
          });
        } else {
          $magpie.addTrialData({
              Stimulus: this.$el.querySelector(".stimulus_id").value,
              // Condition: this.$el.querySelector(".condition_id").value,
              ItemId: this.$el.querySelector(".item_id").value,
              PageId: this.$el.querySelector(".page_id").value,
              Index: this.currentIndex,
              mousePositionX: this.mousePosition.x,
              mousePositionY: this.mousePosition.y,
              wordRevealPart: this.$el.querySelector(".wordRealPart").value,
          });
          
        }
      }},
    moveCursor(e) {
      this.hasRead = true;
      let oval = this.$el.querySelector(".oval-cursor");
      // this.$el.querySelector(".oval-cursor").classList.add('grow');
      let x = e.clientX;
      let y = e.clientY;

      const elementAtCursor= document.elementFromPoint(x, y).closest('span');
      if (elementAtCursor){
        oval.classList.add('grow');
        this.currentIndex = elementAtCursor.getAttribute('data-index');
      } else {
        const elementAboveCursor = document.elementFromPoint(x, y-9).closest('span');
        if (elementAboveCursor){
          oval.classList.add('grow');
          this.currentIndex = elementAboveCursor.getAttribute('data-index');
        } else {
          const elementAboveCursor = document.elementFromPoint(x, y-15).closest('span');
          if (elementAboveCursor){
            oval.classList.add('grow');
            this.currentIndex = elementAboveCursor.getAttribute('data-index');
          } else {
            oval.classList.remove('grow');
            this.currentIndex = -1;
          }
        }
      }
      oval.style.left = `${x}px`;
      oval.style.top = `${y-9}px`;

      // console.log(this.currentIndex);
      this.mousePosition.x = e.clientX;
      this.mousePosition.y = e.clientY;
      // this.mousePosition.x = e.offsetX;
      // this.mousePosition.y = e.offsetY;
    },
    handleRCQButton() {
      $magpie.nextSlide();
    this.hasRead = false;
    },

    saveAndDisable() {
    $magpie.saveAndNextScreen();
    this.hasRead = false;
    },

    getScreenDimensions() {
      return {
        window_inner_width: window.innerWidth,
        window_inner_height: window.innerHeight
      };
    }
  },
};
</script>


<style>
  .experiment {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .main_screen {
    isolation: isolate;
    position: relative;
    width: 100%;
    height: auto;
    font-size: 22px;
    line-height: 50px;
    /* font-size: 22px;
    line-height: 50px; */
  }
  .debugResults{
    width: 100%;
  }
  .readingText {
    /* z-index: 1; */
    position: absolute;
    color: white;
    text-align: left;
    font-weight: 550;
    cursor: pointer;
    padding-top: 2%;
    padding-bottom: 2%;
    padding-left: 11%;
    padding-right: 11%;
    align-items: center; 
  }
  button {
    position: absolute;
    bottom: 0;
    left: 50%;
  }
  .oval-cursor {
    position: fixed;
    z-index: 2;
    width: 1px;
    height: 1px;
    transform: translate(-50%, -50%);
    background-color: white;
    mix-blend-mode: difference;
    border-radius: 50%;
    pointer-events: none;
    transition: width 0.5s, height 0.5s;
  } 
  .oval-cursor.grow.blank {
    width: 80px;
    height: 13px;
  }
  .oval-cursor.grow {
    width: 125px;
    height: 40px; 
    border-radius: 50%;
    /* border-radius: 0; */
    /* border: 0.1px solid red;  */
    /* box-shadow: 30px 0 8px -4px rgba(255, 255, 255, 0.1), -30px 0 8px -4px rgba(255, 255, 255, 0.1); */
    background-color: rgba(255, 255, 255, 0.3);
    background-blend-mode: screen;
    pointer-events: none;
    transition: width 0.5s, height 0.5s;
    filter:blur(3px);
  }
  .oval-cursor.grow::before {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 70%;
    height: 70%;
    background-color: white;
    mix-blend-mode: normal;
    border-radius: 50%;
}
  .blurry-layer {
    position: absolute;
    pointer-events: none;
    color: black;
    text-align: left;
    font-weight: 550;
    padding-top: 2%;
    padding-bottom: 2%;
    padding-left: 11%;
    padding-right: 11%;
    align-items: center; 
  }

  span{
    /* height: 100%; */
    /* display: inline-block; */
    text-align: center;
    /* vertical-align: bottom; */
    align-items: center;
    /* background-color: yellow; */
    /* border: 0.1px, solid, black; */
    /* clip-path: inset(0 0 0 0); */
  }


  * {
    user-select: none; /* Standard syntax */
    -webkit-user-select: none; /* Safari */
    -moz-user-select: none; /* Firefox */
    -ms-user-select: none; /* Internet Explorer/Edge */
    }
</style>