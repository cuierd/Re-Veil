<!-- Window is fixed, 102px, pointer cursor, gradual blurry effect on surrounding words. -->
<!--  Comprehension questions appear afterwards in the same slide -->

<template>
  <Experiment title="Mouse tracking for Reading" translate="no">

    <Screen :title="'欢迎'" class="instructions" :validations="{
        SubjectID: {
          minLength: $magpie.v.minLength(2)
        }
      }">
        <!-- <WelcomeScreen /> -->
        <div style="width: 40em; margin: auto;">

        <div style="background-color: lightgrey; padding: 10px;">
            <b> 关于本研究 </b>
        </div>
        <p>
            欢迎参与我们的研究项目。请阅读如下信息。如有任何疑问，请联系我们。
        <br><br>
          <b>研究内容是什么？</b> 您将参与由苏黎世大学数字语言学小组进行的研究。本研究将帮助我们了解人们如何阅读。
        <br><br>
         <b>我需要做什么？</b> 您将通过移动鼠标来阅读一些文本，并回答相关问题。
        <br><br>
          <b>我的权利是什么？</b> 在参与过程中，您可以随时关闭网页退出本研究，无需说明理由，也不会有任何不利影响。
        <br><br>
          <b>我的数据会如何被收集和使用？</b> 当您阅读我们提供的文本时，鼠标在屏幕上的位置会被记录。本研究的数据将保持完全匿名。我们将严格遵守保密原则。
        <br><br>
          <b> 我对数据有何权利？ </b> 您可以随时要求获取、修改、限制或删除您的数据。为此，请联系如下负责人。

        <br><br>
          <b> 联系方式： </b> Cui Ding [cui.ding@uzh.ch] Department of Computational Linguistics, Andreasstr. 15, CH-8050 Zurich, Switzerland <br>
        </p>

        <br>
        <div style="background-color: lightgrey; padding: 10px;">
            <b> 同意书 </b>
        </div>
        <br>
        我，作为参与者，通过点击下方按钮确认： <br>
        <div style="padding-left: 30px">• 我已阅读并理解关于本研究的信息。 </div>
        <div style="padding-left: 30px">• 我符合参与研究的条件，并了解需要遵守的要求和规则。 </div>
        <div style="padding-left: 30px">• 我有足够的时间来决定是否参与。 </div>
        <div style="padding-left: 30px">• 我自愿参与本研究，并同意按照如上所述使用我的个人数据。</div>
        <br>

          <tr>
          <td>请输入您的 Prolific ID 以继续：&nbsp</td><td><input name="TurkID" type="text" class="obligatory" v-model="$magpie.measurements.SubjectID"/></td>
          </tr>
          <tr>

          </tr>
          </div>
          <div v-if="
            $magpie.measurements.SubjectID&&
            !$magpie.validateMeasurements.SubjectID.$invalid
            ">
          <br> 点击下方按钮即表示您同意参与本研究： <br><br>
          <br />
          <button 
            @click="$magpie.addExpData({ SubjectId: $magpie.measurements.SubjectID}); $magpie.nextScreen()">
            继续
          </button>

          </div>
     </Screen>



    <InstructionScreen :title="'说明'" :button-text="'继续'">
<!-- 
      <p>请在实验期间使用“全屏模式”：
        <a href="javascript:void(0)" @click="turnOnFullScreen">全屏模式</a>
      </p>
 -->
      <p></p>
      <p>本研究的灵感来自于豆瓣上一位网友的偶然经历：该网友的打印机出了点问题，打印出来的文字只能显示上面一半或下面一半，然后网友发现仅有一半的文字似乎也不影响阅读。不过，仅有上半部分和仅有下半部分的文字，哪一种更容易阅读呢？
        这个问题引起了众多网友的兴趣和讨论，也被编选入“豆瓣社区小报”。对这个问题的回答可谓见仁见智，但尚无定论。</p>
      <p>汉字作为目前世界上唯一在用的象形文字，究竟哪一部分承担的信息量更大呢？同样充满好奇的我们试图通过本研究来科学地回答这个问题。</p>
      <p>接下来您将阅读一些文本并回答问题。与正常阅读不同的是，这些文本已被模糊处理。为使其清晰可见，请将鼠标移动到文本上：有些文本仅会显示上半部分文字，有些仅显示下半部分，有些会完整显示。请努力阅读或根据部分字体信息猜测并理解文本。阅读完毕后请点击“回答问题”来作答。</p>
      <p>准备好了吗？让我们一起解开这个有趣现象背后的秘密吧！</p>
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
                <template v-for="(word, index) of para.split('')">
                  <span v-if="trial.wordRevealPart==='l'" style="clip-path: inset(50% 0 0 0);" :key="index" :data-index="`${paraIndex}-${index}`">{{word}}</span>
                  <span v-else-if="trial.wordRevealPart==='u'" style="clip-path: inset(0 0 50% 0);" :key="index" :data-index="`${paraIndex}-${index}`">{{word}}</span>
                  <span v-else :key="index" :data-index="`${paraIndex}-${index}`">{{word}}</span>
                </template>
              </div>
            </div>
            <div class="blurry-layer" style="opacity: 0.3; filter: blur(5px); transition: all 0.3s linear 0s;"> 
              <!-- {{trial.text}} -->
              <div v-for="(para, paraIndex) of trial.text.split('@#@')">
                <template v-for="(word, index) of para.split('')">
                  <span v-if="trial.wordRevealPart==='l'" style="clip-path: inset(50% 0 0 0);" :key="index" :data-index="`${paraIndex}-${index}`">{{word}}</span>
                  <span v-else-if="trial.wordRevealPart==='u'" style="clip-path: inset(0 0 50% 0);" :key="index" :data-index="`${paraIndex}-${index}`">{{word}}</span>
                  <span v-else :key="index" :data-index="`${paraIndex}-${index}`">{{word}}</span>
                </template>
              </div>
            </div>
          </template>

          <button style= "bottom: 25%; transform: translate(-50%, -50%)" @click="trial.question_id == null ? saveAndDisable() : handleRCQButton()" :disabled="!hasRead">
                  {{ trial.question_id == null ? '下一页': '回答问题' }}
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
            <button v-if="$magpie.measurements.response" style="bottom: 25%; transform: translate(-50%, -50%)" @click="$magpie.saveAndNextScreen()">
            {{ '下一页' }}
          </button>
        </Slide>

      </Screen>
    </template>
<Screen>
  <p>1. 您觉得只显示文字上半部分和只显示文字下半部分，哪种更容易阅读？</p>
    <MultipleChoiceInput
        :response.sync= "$magpie.measurements.difficulty"
        orientation="horizontal"
        :options="['只显示上半部分', '只显示下半部分', '难易相当']" />
  <br>
  <br>
  <p>2. 您在这个实验中使用了哪种输入设备？</p>
    <MultipleChoiceInput
        :response.sync= "$magpie.measurements.device"
        orientation="horizontal"
        :options="['鼠标', '触控板', '触控笔', '其他']" />
  <br>
  <br>
  <p>3. 您在这个实验中使用了什么电脑操作系统？</p>
    <MultipleChoiceInput
        :response.sync= "$magpie.measurements.os"
        orientation="horizontal"
        :options="['微软Windows', '苹果MacOS', '其它']" />
  <button style= "bottom: 5%; transform: translate(-50%, -50%)" @click="$magpie.saveAndNextScreen();">提交</button>
</Screen>

    <SubmitResultsScreen />
  </Experiment>
</template>

<script>

// Load data from csv files as javascript arrays with objects
// import onestop_zh_practice from '../trials/onestop_zh_practice.tsv';
import onestop_zh from '../trials/onestop_zh.tsv';
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
    font-size: 18px;
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
    font-weight: 450;
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
    width: 50px;
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
    font-weight: 450;
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