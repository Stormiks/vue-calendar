<template>
  <div id="app" class="calendar">
    <div class="calendar__container">
      <div class="calendar__header">
        <button class="btn btn--left-arrow" type="button" @click="prevMonth">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            viewBox="0 0 492 492"
          >
            <path
              d="M198.608,246.104L382.664,62.04c5.068-5.056,7.856-11.816,7.856-19.024c0-7.212-2.788-13.968-7.856-19.032l-16.128-16.12 C361.476,2.792,354.712,0,347.504,0s-13.964,2.792-19.028,7.864L109.328,227.008c-5.084,5.08-7.868,11.868-7.848,19.084 c-0.02,7.248,2.76,14.028,7.848,19.112l218.944,218.932c5.064,5.072,11.82,7.864,19.032,7.864c7.208,0,13.964-2.792,19.032-7.864 l16.124-16.12c10.492-10.492,10.492-27.572,0-38.06L198.608,246.104z"
            />
          </svg>
        </button>

        <div class="calendar--current">
          <div class="calendar--current-day"></div>
          <div class="calendar--current-month">{{ selectedMonthName }} {{ selectedYear }}</div>
        </div>

        <button class="btn btn--right-arrow" type="button" @click="nextMonth">
					<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 492.004 492.004">
						<path d="M382.678,226.804L163.73,7.86C158.666,2.792,151.906,0,144.698,0s-13.968,2.792-19.032,7.86l-16.124,16.12 c-10.492,10.504-10.492,27.576,0,38.064L293.398,245.9l-184.06,184.06c-5.064,5.068-7.86,11.824-7.86,19.028 c0,7.212,2.796,13.968,7.86,19.04l16.124,16.116c5.068,5.068,11.824,7.86,19.032,7.86s13.968-2.792,19.032-7.86L382.678,265 c5.076-5.084,7.864-11.872,7.848-19.088C390.542,238.668,387.754,231.884,382.678,226.804z"/>
					</svg>
				</button>
      </div>
      <div class="calendar__body">
        <div class="calendar__grid--cells">
          <div class="calendar__grid--weekdays">
            <div
              class="weekday__container"
              v-for="(weekday, index) in weekdays"
              :key="`day-name-${index + 1}`"
              :title="weekday"
            >{{ weekday[0] }}{{ generatorWeekdayPrefix(weekday[1], weekday) }}</div>
          </div>

          <div class="calendar__grid--days">
            <div
              class="day__container"
              v-for="(day, index) in days"
              :key="`day-${index}`"
              :id="`calendar-day-${index + 1}_${day}`"
              :class="emptyDaysNumber[index] === day ? `day--empty` : `day--num-${day}`"
              @click="getDayEvent"
            >
						{{ day }}
						</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Calendar",
  props: {
    initialDate: {
      type: String,
      default: null
    },
    offDays: {
      type: Array,
      default: () => {
        return [1, 7]
      }
    },
    fullDays: {
      type: Number,
      default: 42
    }
  },
  data() {
    return {
      // today: new Date(),
      emptyDays: [],
      emptyDaysNumber: [],
      weekdays: null,
      date: null,
      vowel: ["а", "е", "о", "у", "я"]
    };
  },
  computed: {
    prevMonthEmptyDays () {
      this.emptyDays = Array(((this.startWeekDayOfMonth - 1) + 7) % 7).fill(null)
      let emptyDays = this.emptyDays
      return emptyDays
    },
    nextMonthEmptyDays () {

    },
    days () {
      console.log(this.prevMonthEmptyDays)
      let emptyDays = this.getPrevMonthEmptyDays(this.prevMonthEmptyDays)
      this.emptyDaysNumber = emptyDays

			let days = Array(this.numberOfDays).fill().map((item, index) => {
        console.log(index)
        return new Date(this.selectedYear, this.selectedMonth, index + 1).getDate()
      })

			let concatDays = emptyDays.concat(days)
			console.log('Пустые дни', this.prevMonthEmptyDays)
			console.log('Дней в месяце', days)
			console.log('ОБъединенные дни', concatDays)
			return concatDays
    },
    startWeekDayOfMonth () {
      console.log(new Date(this.date.getFullYear(), this.date.getMonth(), 1).getDay())
      return new Date(this.date.getFullYear(), this.date.getMonth(), 1).getDay()
    },
    // Количество дней в выбранном месяце
    numberOfDays () {
			let date = new Date(this.date.getFullYear(), this.date.getMonth() + 1, 0).getDate()
			console.log(date)
      return date;
    },
    selectedMonth () {
      return this.date.getMonth();
    },
    selectedMonthName () {
      let localDate = this.date.toLocaleString("ru-RU", { month: "long" })
      return localDate
    },
    selectedYear () {
      return this.date.getFullYear();
    }
  },
  methods: {
    getPrevMonthEmptyDays (emptyDays) {
      console.log(emptyDays)
      return emptyDays.map((item, index) => {
        let day = new Date(this.selectedYear, this.selectedMonth, -index).getDate()
        console.log(day)
        return day
      }).sort()
    },
    prevMonth () {
      this.date = new Date(this.selectedYear, this.selectedMonth - 1, 1)
    },
    nextMonth () {
      this.date = new Date(this.selectedYear, this.selectedMonth + 1, 1)
    },
    generatorWeekdayPrefix(weekdayChar, weekday) {
      let char;

      this.vowel.forEach((val, index, arr) => {
        if (val === weekdayChar) {
          return (char = weekday[2]);
        }
      });

      if (!char) char = weekday[1];

      return char;
    },
    generatorWeekdays() {
      let weekdays = [
        "Понедельник",
        "Вторник",
        "Среда",
        "Четверг",
        "Пятница",
        "Суббота",
        "Воскресенье"
      ];

      let vowel = ["а", "е", "о", "у", "я"];

      for (let i = 2; i <= 1; i++) {
        console.log(weekdays);
        let first = weekdays.shift();
        console.log(first);
        first.push(first);
      }

      return weekdays;
    },
    getDayEvent (e) {
      console.log(e)
    },
    today () {
      return new Date().getDate()
    }
  },
  beforeMount() {
    this.date = new Date();
    this.weekdays = this.generatorWeekdays();
  },
  mounted() {
    // console.dir(this.today());
  }
};
</script>

<style lang="less">
html,
body {
  margin: 0;
  height: 100%;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn {
	&:hover {
		cursor: pointer;
	}
}

.day--empty {
  cursor: normal;
  color: lighten(grey, 20%);
  background-color: lighten(grey, 40%);
}

div[class*="day--num-"] {
  transition: .15s;
  
  &:hover {
    cursor: pointer;
    background-color: lighten(grey, 40%);
  }
}

.weekday__container {
  font-family: Arial;
  padding: 5px 3px;
}

.day__container {
  border: 0.5px solid green;
  border-radius: 5px;
}

.calendar {
  display: flex;
  align-items: center;
  justify-content: center;

  &__container {
    width: 500px;
  }

  &--current {
		display: flex;
		justify-content: center;
		flex-grow: .1;

    &-day {
		}
		
    &-month {
			text-transform: capitalize;
    }
  }

  &__header {
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 5px;
		padding-bottom: 15px;
		
		.btn {
			border: none;
			border-radius: 10px;
			padding-top: 5px;
			padding-bottom: 5px;

			svg {
				display: block;
				height: 20px;
				transition: .12s;
			}

			&:hover {
				background-color: darken(#fff, 14%);
				box-shadow: 0 0 2px 0 rgba(#000, 70%);

				svg {
					fill: #5f9ea0;
					stroke: #000;
					stroke-width: 2px;
				}
			}
		}
  }

  &__body {
  }

  &__grid {
    &--cells {
      border: 0.5px solid #000;
    }

    &--weekdays {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
			grid-gap: 5px;
			padding: 5px;

      .weekday__container {
        place-self: center;
      }
    }

    &--days {
			display: grid;
      grid-gap: 5px;
      grid-template-rows: repeat(6, 1fr);
			grid-template-columns: repeat(7, 1fr);
      padding: 5px;
      
      > :nth-child(7n + 6),
      > :nth-child(7n + 7) {
        background-color: #faebd7;
      }

			.day__container {
				height: 60px;
				display: flex;
				justify-content: center;
			}
    }
  }
}
</style>

