<template>
    <div class="outerDateContainer" tabindex="0" @keydown.enter="setIsShowDatePicker(true)">
      <div v-if="textfield" class="textFieldContainer" @click="setIsShowDatePicker">
        <input :value="computedStringValue">
          <!-- <template v-for="(index, name) in $slots" v-slot:[name]>
            <slot :name="name" />
          </template> -->
        </input>
      </div>
      <Transition name="scaleIn" mode="out-in">
        <div v-if="getIsShowDatePicker()" class="dateContainer" :class="{ textField: textfield }">
          <!-- Header -->
          <div v-if="header" class="dateHeaderContainer">
            <Transition :name="slideAnimationName" mode="out-in">
              <p
                :key="dates[yearIndex][monthIndex][dayIndex].getFullYear()"
                class="yearString"
                tabindex="0"
                @click="setIsShowYearsPopUp(true)"
                @keydown.enter="setIsShowYearsPopUp(true)"
              >
                {{ dates[yearIndex][monthIndex][dayIndex].getFullYear() }}
              </p>
            </Transition>
            <Transition name="slideUp" mode="out-in">
              <div v-if="range && selectedDate.length === 2" :key="selectedDate.length" class="dateRangeStringsContainer">
                <p>
                  {{ selectedDate[0].toLocaleDateString() }}
                </p>
                ->
                <p>
                  {{ selectedDate[1].toLocaleDateString() }}
                </p>
              </div>
              <p v-else-if="selectedDate.length === 1" :key="selectedDate[0].toDateString()" class="dateString">
                {{ this.renderDate(selectedDate[0]) }}
              </p>
              <p v-else :key="selectedDate.length" class="dateString">{{ selectedDate.length }} Selected</p>
            </Transition>
          </div>
  
          <!-- Select Years popup -->
          <Transition name="slideUpDown" mode="in-out">
            <div v-if="isShowYearPopUp" :key="isShowYearPopUp" class="yearsPopUpContainer" :class="{ noHeader: !header }">
              <div ref="yearsRef" class="years">
                <div
                  v-for="(year, index) in dates"
                  :key="index"
                  tabindex="0"
                  class="yearContainer"
                  :class="{
                    selected: dates[index][0][0].getFullYear() === selectedDate[0].getFullYear(),
                  }"
                  @click="setSelectedYear(index)"
                  @keydown.enter="setSelectedYear(index)"
                >
                  <p>
                    {{ dates[index][0][0].getFullYear() }}
                  </p>
                </div>
              </div>
            </div>
          </Transition>
  
          <!-- Select Month popup -->
          <Transition name="slideUpDown" mode="in-out">
            <div
              v-if="isShowMonthsPupUp"
              :key="isShowMonthsPupUp"
              class="monthsPopUpContainer"
              :class="{ noHeader: !header }"
            >
              <div class="monthsPopUpYearSelector">
                <div class="arrowButton" @click="prevYear">
                  <
                </div>
                <Transition :name="slideAnimationName" mode="out-in">
                  <p
                    :key="dates[yearIndex][monthIndex][dayIndex].getFullYear()"
                    class="yearString"
                    tabindex="0"
                    @click="setIsShowYearsPopUp(true)"
                    @keydown.enter="setIsShowYearsPopUp(true)"
                  >
                    {{ dates[yearIndex][monthIndex][dayIndex].getFullYear() }}
                  </p>
                </Transition>
                <div class="arrowButton" @click="nextYear">
                  >
                </div>
              </div>
              <Transition :name="slideAnimationName" mode="out-in">
                <div class="months" :key="yearIndex">
                  <div
                    v-for="(month, index) in dates[yearIndex]"
                    :key="index"
                    tabindex="0"
                    class="monthContainer"
                    :class="{
                      selected:
                        dates[yearIndex][index][0].toLocaleString('default', {
                          month: 'long',
                          year: 'numeric',
                        }) ===
                        selectedDate[0].toLocaleString('default', {
                          month: 'long',
                          year: 'numeric',
                        }),
                    }"
                    @click="setSelectedMonth(index)"
                    @keydown.enter="setSelectedMonth(index)"
                  >
                    <p>
                      {{
                        dates[yearIndex][index][0].toLocaleString("default", {
                          month: "long",
                        })
                      }}
                    </p>
                  </div>
                </div>
              </Transition>
            </div>
          </Transition>
  
          <!-- Date selector -->
          <div class="datePickerContainer">
            <div class="arrowsMonthContainer">
              <div class="arrowButton" @click="prevMonth">
                <
              </div>
              <Transition :name="slideAnimationName" mode="out-in">
                <p
                  :key="
                    dates[yearIndex][monthIndex][dayIndex].toLocaleString('default', {
                      month: 'long',
                    })
                  "
                  class="monthString"
                  @click="
                    () => {
                      if (header) setIsShowMonthsPopUp(true);
                      if (!header) setIsShowYearsPopUp(true);
                    }
                  "
                >
                  {{ getMonthAndOrYear() }}
                </p>
              </Transition>
              <div class="arrowButton" @click="nextMonth">
                >
              </div>
            </div>
            <div class="weekdayTextHeader">
              <p v-for="(weekday, index) in weekdays" :key="index">
                {{ weekday }}
              </p>
            </div>
            <Transition :name="slideAnimationName" mode="out-in">
              <div :key="monthIndex" class="daysInMonthContainer">
                <div
                  v-for="day in dates[yearIndex][monthIndex]"
                  :key="day.toLocaleString()"
                  class="dayContainer"
                  :class="{
                    selected: isSelected(day),
                    inRange: isDateInRange(day),
                    today: day.toDateString() === new Date().toDateString(),
                    monday: day.getDay() === 1,
                    tuesday: day.getDay() === 2,
                    wednesday: day.getDay() === 3,
                    thursday: day.getDay() === 4,
                    friday: day.getDay() === 5,
                    saturday: day.getDay() === 6,
                    sunday: day.getDay() === 0,
                  }"
                  tabindex="0"
                  @keydown.enter="setSelectedDate(day)"
                  @click="setSelectedDate(day)"
                >
                  <p :class="{disabled: shouldBeDisabled(day)}" class="dayNumber">{{ day.getDate() }}</p>
                </div>
              </div>
            </Transition>
            <!-- Buttons -->
            <div class="buttonsContainer" v-if="buttons">
              <button @click.prevent="isShowDatePicker = false" size="xs">Ok</button>
            </div>
          </div>
        </div>
      </Transition>
    </div>
  </template>
  
  <script>
  import {eachDayOfInterval, isSameMonth, isSameYear, isBefore, isEqual} from "date-fns";
  export default {
    name: "TnDatePicker",
    props: {
      /**
       *  Binding value
       */
      value: {
        type: Array,
        default() {
          return [new Date()];
        },
      },
      /**
       * Override locale used when rendering dates
       */
      locale: { type: String, default: "nb-NO" },
      /**
       * Override locale options when rendering date.
       *
       * See:
       *   https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleDateString#options
       *
       * for available options.
       */
      localeOptions: { type: Object, default: {
          weekday: "long",
          month: "long",
          day: "numeric",
          year: "numeric"
        }
      },
      /**
       * Sets a pre selected date
       */
      preSelectedDate: { type: Array, default: null },
      /**
       * Will disable all previous dates of current or pre selected date
       */
      disablePreviousDates: { type: Boolean, default: false },
      /**
       * Will disable every date expect dates from this array
       */
      allowedDates: { type: Array, default: null },
      /**
       * Will disable only the dates from this array
       */
      disabledDates: { type: Array, default: null },
      /**
       * Sets ability to select multiple dates
       */
      multiple: { type: Boolean, default: false },
      /**
       * Sets ability to select a range between two dates
       */
      range: { type: Boolean, default: false },
      /**
       * Should component be rendered as a text field with popup of picker
       */
      textfield: { type: Boolean, default: true },
      /**
       * Show or hide header
       */
      header: { type: Boolean, default: true },
      /**
       * Show buttons
       */
      buttons: { type: Boolean, default: true },
      /**
       * Automatically close picker when date selected. NB only works on single select
       */
      autoClose: { type: Boolean, default: false },
      /**
       * Textfield label
       */
      label: { type: String, default: "" },
      /**
       * Size of the input field
       * @values s, m, l
       */
      size: {
        type: String,
        default: "s",
        validator: function (value) {
          return ["s", "m", "l"].includes(value.toLowerCase());
        },
      },
      /**
       * Sets amount of years the picker will go to the past from today's year
       */
      yearAmountPast: { type: Number, default: 50 },
      /**
       * Sets amount of years the picker will go to the future from today's year
       */
      yearAmountFuture: { type: Number, default: 50 },
    },
    data() {
      return {
        monthIndex: 1,
        yearIndex: 1,
        dayIndex: 1,
        selectedDate: [new Date()],
        slideAnimationName: "slideRight",
        isShowMonthsPupUp: false,
        isShowYearPopUp: false,
        dates: [],
        weekdays: ["M", "T", "W", "T", "F", "S", "S"],
        isShowDatePicker: false,
      };
    },
    computed: {
      computedStringValue() {
        let value = "";
        if (this.multiple && this.selectedDate.length > 1) {
          this.selectedDate.forEach((date) => {
            value += `${this.renderDate(date)},  `;
          });
          return value;
        }
        if (this.range && this.selectedDate.length > 1) {
          value = `${this.renderDate(this.selectedDate[0])} ~ ${this.renderDate(this.selectedDate[1])}`;
          return value;
        }
        return this.renderDate(this.selectedDate[0]);
      },
    },
    watch: {
      value() {
        this.selectedDate = this.value;
      },
      selectedDate() {
        this.$emit("input", this.selectedDate);
      },
      isShowYearPopUp(isShowYearPopUp) {
        if (isShowYearPopUp) {
          this.scrollToSelectedYear();
          this.setIsShowMonthsPopUp(true);
        }
      },
      textfield(value) {
        if (!value) {
          this.isShowDatePicker = true;
          return;
        }
        this.isShowDatePicker = false;
      },
    },
    created() {
      this.dates = this.generateDates();
      if (this.preSelectedDate) {
        this.selectedDate = this.preSelectedDate;
      }
      this.setIndexesForSelected();
    },
    beforeMount() {
      window.addEventListener("click", (e) => {
        if (!this.$el.contains(e.target)) {
          this.isShowDatePicker = false;
        }
      });
    },
    beforeUnmount() {
      window.removeEventListener("click", (e) => {
        if (!this.$el.contains(e.target)) {
          this.isShowDatePicker = false;
        }
      });
    },
    methods: {
      // generates 3D array of dates
      generateDates() {
        const today = new Date();
        const days = eachDayOfInterval({
          start: new Date(today.getFullYear() - this.yearAmountPast, 0, 1),
          end: new Date(today.getFullYear() + this.yearAmountFuture, 12, 0),
        });
        const months = [];
        let tempMonths = [];
        for (let i = 0; i < days.length; i++) {
          const day = days[i];
          const nextDay = days[i + 1];
          if (!isSameMonth(day, nextDay)) {
            tempMonths.push(day);
            if (tempMonths.length > 0) {
              months.push(tempMonths);
            }
            tempMonths = [];
          } else {
            tempMonths.push(day);
          }
        }
        const years = [];
        let tempYears = [];
        for (let i = 0; i < months.length; i++) {
          const monthArray = months[i] || [];
          const nextMonthArray = months[i + 1] || [];
          const firstDayOfMonth = monthArray[0];
          const nextFirstDayOfMonth = nextMonthArray[0];
          if (!isSameYear(firstDayOfMonth, nextFirstDayOfMonth)) {
            tempYears.push(monthArray);
            if (tempYears.length > 0) {
              years.push(tempYears);
            }
            tempYears = [];
          } else {
            tempYears.push(monthArray);
          }
        }
        return years;
      },
      getIsShowDatePicker() {
        if (!this.textfield) {
          return true;
        }
        return this.isShowDatePicker;
      },
      getMonthAndOrYear() {
        const monthString = this.dates[this.yearIndex][this.monthIndex][this.dayIndex].toLocaleString("default", {
          month: "long",
        });
        const yearString = this.dates[this.yearIndex][this.monthIndex][this.dayIndex].getFullYear();
        if (this.header) {
          return monthString;
        }
        return `${monthString} ${yearString}`;
      },
      setIsShowDatePicker(value) {
        if (typeof value !== "boolean") {
          this.isShowDatePicker = !this.isShowDatePicker;
          return;
        }
        this.isShowDatePicker = value;
      },
      setSelectedMonth(index) {
        if (index < this.monthIndex) {
          this.slideAnimationName = "slideRight";
        } else {
          this.slideAnimationName = "slideLeft";
        }
        this.monthIndex = index;
        this.setIsShowMonthsPopUp(false);
      },
      setIsShowMonthsPopUp(isShowMonth) {
        this.isShowMonthsPupUp = isShowMonth;
      },
      setSelectedYear(index) {
        if (index < this.yearIndex) {
          this.slideAnimationName = "slideRight";
        } else {
          this.slideAnimationName = "slideLeft";
        }
        this.yearIndex = index;
        this.setIsShowYearsPopUp(false);
      },
      setIsShowYearsPopUp(isShowYear) {
        this.isShowYearPopUp = isShowYear;
      },
      renderDate(date) {
        return date.toLocaleDateString(this.locale, this.localeOptions);
      },
      scrollToSelectedYear() {
        this.$nextTick(() => {
          const height = this.$refs.yearsRef.scrollHeight;
          const indexHeight = height / this.dates.length;
          let scrollAmountY = this.yearIndex * indexHeight;
          scrollAmountY = scrollAmountY - 2 * indexHeight;
          this.$refs.yearsRef.scroll({
            top: scrollAmountY,
          });
        });
      },
      setSelectedDate(date) {
        if (!this.shouldBeDisabled(date)) {
          if (this.multiple) {
            this.handleMultipleSelect(date);
            return;
          }
          if (this.range) {
            this.handleRangeSelect(date);
            return;
          }
          this.handleSingleSelect(date);
        }
      },
      handleSingleSelect(date) {
        this.selectedDate.splice(0, this.selectedDate.length, date);
        if (this.autoClose) {
          this.isShowDatePicker = false;
        }
      },
      handleMultipleSelect(date) {
        let exists = false;
        this.selectedDate.forEach((selectedDate) => {
          if (date.toDateString() === selectedDate.toDateString() && this.selectedDate.length > 1) {
            const selectedDateIndex = this.selectedDate.indexOf(selectedDate);
            this.selectedDate.splice(selectedDateIndex, 1);
            exists = true;
          }
        });
        if (!exists && date.toDateString() === this.selectedDate[0].toDateString()) return;
        if (!exists) {
          this.selectedDate.push(date);
        }
      },
      handleRangeSelect(date) {
        if (this.selectedDate.length < 2) {
          this.selectedDate.push(date);
          this.selectedDate.sort((a, b) => a - b);
          return;
        }
        if (this.selectedDate.length === 2) {
          this.selectedDate.splice(0, this.selectedDate.length, date);
        }
      },
      isDateInRange(day) {
        if (!this.range) {
          return false;
        }
        const startDate = this.selectedDate[0];
        const endDate = this.selectedDate[1];
        if (startDate < day && endDate > day) {
          return true;
        }
        return false;
      },
      getDatesInRange() {
        let datesInRange = this.selectedDate;
        if (this.selectedDate.length === 2 && this.range) {
          const startDate = this.selectedDate[0];
          const endDate = this.selectedDate[1];
          datesInRange = eachDayOfInterval({
            start: startDate,
            end: endDate,
          });
        }
        return datesInRange;
      },
      isSelected(day) {
        let result = false;
        this.selectedDate.forEach((date) => {
          if (day.toDateString() === date.toDateString()) {
            result = true;
          }
        });
        return result;
      },
      setIndexesForSelected() {
        for (let i = 0; i < this.dates.length; i++) {
          const months = this.dates[i];
          for (let j = 0; j < months.length; j++) {
            const days = months[j];
            for (let k = 0; k < days.length; k++) {
              const day = days[k];
              if (day.toDateString() === this.selectedDate[0].toDateString()) {
                this.dayIndex = k;
                this.monthIndex = j;
                this.yearIndex = i;
              }
            }
          }
        }
      },
      handleFocusOut() {
        this.setIsShowDatePicker(false);
      },
      prevMonth() {
        this.slideAnimationName = "slideRight";
        if (this.dates[this.yearIndex][this.monthIndex - 1]) {
          this.monthIndex--;
        } else if (
          this.dates[this.yearIndex - 1] &&
          this.dates[this.yearIndex - 1][this.dates[this.yearIndex - 1].length - 1]
        ) {
          this.monthIndex = this.dates[this.yearIndex - 1].length - 1;
          this.yearIndex--;
        }
      },
      nextMonth() {
        this.slideAnimationName = "slideLeft";
        if (this.dates[this.yearIndex][this.monthIndex + 1]) {
          this.monthIndex++;
          this.dayIndex = this.dates[this.yearIndex][this.monthIndex][this.dayIndex] ? this.dayIndex : 1
        } else if (this.dates[this.yearIndex + 1] && this.dates[this.yearIndex + 1][0]) {
          this.monthIndex = 0;
          this.yearIndex++;
        }
      },
      prevYear() {
        this.slideAnimationName = "slideRight";
        if (this.dates[this.yearIndex - 1]) {
          this.yearIndex--;
        }
      },
      nextYear() {
        this.slideAnimationName = "slideLeft";
        if (this.dates[this.yearIndex + 1]) {
          this.yearIndex++;
        }
      },
      shouldBeDisabled(date) {
        const today = new Date()
        today.setHours(0, 0, 0, 0)
        const currentEarliest = this.preSelectedDate ? this.preSelectedDate[0] : today
        let disableDate = false
  
        if (this.disablePreviousDates) {
            isBefore(date, currentEarliest) ? disableDate = true : disableDate
        }
  
        if (this.disabledDates) {
          this.disabledDates.map(date => date.setHours(0, 0, 0, 0))
          this.disabledDates.some(disabledDate => isEqual(disabledDate, date)) ? disableDate = true : disableDate
        }
  
        if (this.allowedDates) {
          this.allowedDates.map(date => date.setHours(0, 0, 0, 0))
          this.allowedDates.some(disabledDate => isEqual(disabledDate, date)) ? disableDate = false : disableDate = true
        }
  
        return disableDate
      },
    },
    mounted() {
      this.selectedDate = this.value;
    }
  };
  </script>
  
  <style scoped lang="scss">
  .outerDateContainer {
    position: relative;
    width: 100%;
    font-family: Arial;
    p {
      margin: 0;
    }
    .dateContainer.textField {
      position: absolute;
      z-index: 2;
    }
    .dateContainer {
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      background-color: lightgray;
      border-radius: 4px;
      overflow: hidden;
      position: relative;
      width: 280px;
      .dateHeaderContainer {
        width: 100%;
        background-color: blue;
        border-radius: 4px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        .yearString {
          font-size: 1.1em;
          color: white;
          max-width: fit-content;
          margin: 10px 0px 5px 20px;
        }
        .yearString:hover {
          cursor: pointer;
          opacity: 1 !important;
        }
        .dateString {
          font-size: 1.4em;
          color: white;
          max-height: 28px;
          min-height: 28px;
          margin: 0 20px 10px 20px;
        }
        .dateRangeStringsContainer {
          display: flex;
          justify-content: space-between;
          align-items: center;
          font-size: 1.3em;
          margin: 0 20px 10px 20px;
          color: white;
          max-height: 28px;
          min-height: 28px;
          p {
            margin: 0;
          }
        }
      }
      .datePickerContainer {
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
        .arrowsMonthContainer {
          width: 100%;
          display: flex;
          justify-content: space-between;
          align-items: center;
          margin: 4px 4px 0 4px;
          p {
            margin: 0;
          }
          .arrowButton:first-child {
            margin-left: 20px;
          }
          .arrowButton:last-child {
            margin-right: 20px;
          }
  
          .monthString {
            font-size: 1.2em;
          }
          .monthString:hover {
            cursor: pointer;
            color: blue;
          }
        }
        .weekdayTextHeader {
          display: grid;
          width: 100%;
          grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
          margin: 10px 0 5px 0;
          width: 98%;
          p {
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            opacity: 0.4;
          }
          :last-child {
            color: red;
            opacity: 0.9;
          }
        }
        .daysInMonthContainer {
          display: grid;
          grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
          grid-template-rows: 1fr 1fr 1fr 1fr 1fr 1fr;
          flex-wrap: wrap;
          height: 100%;
          width: 98%;
          align-items: flex-end;
          .dayContainer {
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 4px;
            margin: 2px 2px;
            height: 34px;
            p {
              height: 30px;
              width: 30px;
              box-sizing: border-box;
              display: flex;
              justify-content: center;
              align-items: center;
  
              &.disabled {
                color: rgba(0,0,0,0.2);
              }
            }
          }
          .monday {
            grid-column-start: 1;
            grid-column-end: 1;
          }
          .tuesday {
            grid-column-start: 2;
            grid-column-end: 2;
          }
          .wednesday {
            grid-column-start: 3;
            grid-column-end: 3;
          }
          .thursday {
            grid-column-start: 4;
            grid-column-end: 4;
          }
          .friday {
            grid-column-start: 5;
            grid-column-end: 5;
          }
          .saturday {
            grid-column-start: 6;
            grid-column-end: 6;
          }
          .sunday {
            grid-column-start: 7;
            grid-column-end: 7;
          }
          .dayContainer:hover {
            background-color: tomato;
            cursor: pointer;
          }
          .inRange {
            background-color: rgba(169, 225, 255, 0.5);
          }
          .selected {
            background-color: blue;
            color: white;
            animation: scaleUpDown 250ms ease;
          }
          .today {
            p {
              border-bottom: 3px solid blue;
              padding-top: 2px;
            }
          }
        }
        .buttonsContainer {
          display: flex;
          justify-content: flex-end;
          width: 100%;
          button {
            margin: 0 10px 10px 0;
          }
        }
      }
      .monthsPopUpContainer.noHeader {
        height: 100%;
      }
      .monthsPopUpContainer {
        position: absolute;
        width: 100%;
        background-color: lightgray;
        display: flex;
        flex-direction: column;
        justify-content: center;
        bottom: 0;
        height: 80%;
        z-index: 2;
        .monthsPopUpYearSelector {
          display: flex;
          justify-content: space-between;
          align-items: center;
          margin-top: 5px;
          .arrowButton:first-child {
            margin-left: 20px;
          }
          .arrowButton:last-child {
            margin-right: 20px;
          }
          p:hover {
            cursor: pointer;
            color: blue;
          }
          p {
            font-size: 1.1em;
          }
        }
        .closeMonthsContainer {
          display: flex;
          justify-content: center;
          align-items: center;
          height: 10%;
          margin-bottom: 10px;
          svg:hover {
            cursor: pointer;
            color: blue;
          }
        }
  
        .months {
          display: grid;
          grid-template-columns: 33% 33% 33%;
          grid-template-rows: 1fr 1fr 1fr 1fr;
          height: 100%;
          margin-bottom: 5px;
          .monthContainer {
            display: flex;
            justify-content: center;
            align-items: center;
            margin: 8px 5px;
            border-radius: 4px;
            padding: 0 5px;
            font-size: 0.9em;
          }
          .selected {
            background-color: blue;
            color: white;
          }
          .monthContainer:hover {
            background-color: tomato;
            color: black;
            cursor: pointer;
          }
        }
      }
      .yearsPopUpContainer.noHeader {
        height: 100%;
      }
      .yearsPopUpContainer {
        position: absolute;
        width: 100%;
        background-color: lightgray;
        border-radius: 20px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        bottom: 0;
        height: 80%;
        z-index: 3;
        .closeYearsContainer {
          display: flex;
          position: absolute;
          width: 100%;
          min-height: 40px;
          bottom: 0;
          justify-content: center;
          align-items: center;
          height: 10%;
          background: linear-gradient(
            180deg,
            rgba(0, 0, 0, 0) 0%,
            rgba(0, 0, 0, 0) 0%,
            rgba(0, 0, 0, 0) 0%,
          );
          svg:hover {
            cursor: pointer;
            color: blue;
          }
        }
        .years::-webkit-scrollbar {
          display: none; /* Safari and Chrome */
        }
        .years {
          max-height: 100%;
          min-height: 100%;
          overflow-y: scroll;
          display: flex;
          flex-direction: column;
          min-width: 100%;
          -ms-overflow-style: none; /* Internet Explorer 10+ */
          scrollbar-width: none; /* Firefox */
          .yearContainer {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 50px;
            border-radius: 4px;
          }
          .yearContainer:hover {
            cursor: pointer;
            background-color: tomato;
            color: black;
          }
          .selected {
            background-color: blue;
            color: white;
          }
        }
      }
    }
    .arrowButton {
      cursor: pointer;
      border-radius: 50%;
      max-width: 30px;
      min-width: 30px;
      max-height: 30px;
      min-height: 30px;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .arrowButton:hover {
      color: blue;
      transform: scale(1.3);
    }
    @keyframes scaleUpDown {
      0% {
        transform: scale(1);
      }
      50% {
        transform: scale(1.1);
      }
      100% {
        transform: scale(1);
      }
    }
    // Transitions
    .slideLeft-enter-active {
      transition: all 200ms ease;
    }
    .slideLeft-leave-active {
      transition: all 200ms ease;
    }
    .slideLeft-enter {
      transform: translateX(80px);
      opacity: 0 !important;
    }
    .slideLeft-leave-to {
      transform: translateX(-80px);
      opacity: 0 !important;
    }
  
    .slideRight-enter-active {
      transition: all 200ms ease;
    }
    .slideRight-leave-active {
      transition: all 200ms ease;
    }
    .slideRight-enter {
      transform: translateX(-80px);
      opacity: 0 !important;
    }
    .slideRight-leave-to {
      transform: translateX(80px);
      opacity: 0 !important;
    }
  
    .slideUp-enter-active {
      transition: all 150ms ease;
    }
    .slideUp-leave-active {
      transition: all 150ms ease;
    }
    .slideUp-enter {
      transform: translateY(20px);
      opacity: 0 !important;
    }
    .slideUp-leave-to {
      transform: translateY(-20px);
      opacity: 0 !important;
    }
  
    .slideUpDown-enter-active {
      transition: all 150ms ease;
    }
    .slideUpDown-leave-active {
      transition: all 150ms ease;
    }
    .slideUpDown-enter {
      transform: translateY(100px);
      opacity: 0 !important;
    }
    .slideUpDown-leave-to {
      transform: translateY(100px);
      opacity: 0 !important;
    }
  
    .scaleIn-enter-active {
      transition: all 250ms ease;
    }
    .scaleIn-leave-active {
      transition: all 250ms ease;
    }
    .scaleIn-enter {
      transform-origin: top left;
      transform: scale(0);
      opacity: 0;
    }
    .scaleIn-leave-to {
      transform-origin: top left;
      transform: scale(0);
      opacity: 0;
    }
}
  </style>
  