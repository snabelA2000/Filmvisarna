<template>
  <div class="availableShowing">
    <h1 class="getTickets">Get tickets</h1>
    <ShowingsFilter
      :options="['Show All', 'Theatre 1', 'Theatre 2']"
      :default="'Filter by Theatre'"
      @input="addFilterItem($event)"
    />
    <div class="calenContainer">
      <img class="calenIcon" @click="show()" src="../pictures/calendar.svg" />
    </div>
    <div ref="draggableContainer" id="draggable-container">
      <div id="draggable-header" @mousedown="dragMouseDown">
        <transition
          name="custom-classes-transition"
          mode="out-in"
          enter-active-class="animate__animated animate__zoomIn"
          leave-active-class="animate__animated animate__zoomOut"
        >
          <Calendar v-if="showCalen" v-on:datePicked="what" />
        </transition>
      </div>
    </div>
  </div>

  <div
    class="space"
    v-if="showings.length && new Date(today()) > new Date() - 86400000"
  >
    <div class="showingList" v-for="showing of filteredShowings" :key="showing">
      <div class="anotherFormattingDiv">
        <div v-if="member">
          <button
            class="bookButton grow"
            @click="
              $router.push(
                '/chosen-movie/' + showing.title + '/booking/' + showing.id
              )
            "
          >
            Book
          </button>
        </div>
        <div v-else>
          <button class="popup bookButton" @click="popUp()">
            Book
            <span class="popuptext" id="myPopup"
              >You need to Sign In First</span
            >
          </button>
        </div>
        <h3 class="noTopMargin" v-if="showings.length">
          <span>{{ showing.date }}</span
          ><span>{{ showing.time }}</span
          ><span>{{ showing.theatre }}</span>
          <span>{{ showing.salon }}</span>
        </h3>
      </div>
    </div>
  </div>
  <div class="noMovie space" v-else>
    --- NO BOOKINGS AVAILABLE FOR THE SELECTED DATE ---
  </div>
</template>

<script>
import ShowingsFilter from "./ShowingsFilter.vue";
import Calendar from "./Calendar.vue";
import About from "../views/About.vue";

export default {
  components: {
    Calendar,
    ShowingsFilter,
    About,
  },

  data() {
    return {
      signIn: false,
      showCalen: false,
      hello: 0,
      bye: 0,
      pos: {
        clientX: undefined,
        clientY: undefined,
        movX: 0,
        movY: 0,
      },
    };
  },

  methods: {
    popUp() {
      const popup = document.getElementById("myPopup");
      popup.classList.toggle("show");
    },
    goSignIn() {
      return (this.signIn = !this.signIn);
    },
    show() {
    
      return (this.showCalen = !this.showCalen);
    },
    what(dayPicked, monthPicked) {
      this.hello = dayPicked;
      this.bye = monthPicked;
    },
    today() {
      const x = new Date();
      const year = x.getFullYear();
      const month = this.bye || x.getMonth() + 1;
      const day = this.hello || x.getDate();
      const date =
        year +
        "-" +
        (month < 10 ? "0" : "") +
        month +
        "-" +
        (day < 10 ? "0" : "") +
        day;
      return date;
    },

    addFilterItem(itemName) {
      this.$store.state.showingsFilterItem = itemName;
    },
    dragMouseDown: function (event) {
      event.preventDefault();
      // get the mouse cursor position at startup:
      this.pos.clientX = event.clientX;
      this.pos.clientY = event.clientY;
      document.onmousemove = this.elementDrag;
      document.onmouseup = this.closeDragElement;
    },
    elementDrag: function (event) {
      event.preventDefault();
      this.pos.movX = this.pos.clientX - event.clientX;
      this.pos.movY = this.pos.clientY - event.clientY;
      this.pos.clientX = event.clientX;
      this.pos.clientY = event.clientY;
      // set the element's new position:
      this.$refs.draggableContainer.style.top =
        this.$refs.draggableContainer.offsetTop -
        this.pos.movY +
        "px";
      this.$refs.draggableContainer.style.left =
        this.$refs.draggableContainer.offsetLeft -
        this.pos.movX +
        "px";
    },
    closeDragElement() {
      document.onmouseup = null;
      document.onmousemove = null;
    },
  },

  computed: {
    member() {
  
      return this.$store.state.member;
    },

    title() {
      // get showing id from url parameter
      return this.$route.params.title.replaceAll("-", " ");
    },

    filteredShowings() {
      let item = this.$store.state.showingsFilterItem;
      if (
        item === null ||
        item === "Show All" ||
        item === "Filter by Theatre"
      ) {
        return this.showings;
      } else {
        return this.showings.filter((showing) =>
          showing.theatre.includes(item)
        );
      }
    },

    showings() {
      return this.$store.state.showings
        .filter((obj) => obj.title == this.title)
        .filter((obj) => obj.date == this.today());
    },
  },
};
</script>

<style scoped>
.showingsListContainer {
  height: 330px;
}

.space {
  grid-row: 8;
  grid-column: 1;
  padding-top: 110px;
  height: 300px;
}
.signInButton {
  appearance: none;
  outline: none;
  border: none;
  background: none;
  cursor: pointer;
  color: rgb(116, 109, 98);
  padding: 16px 18px;
  font-size: 20px;
  z-index: 100;
}
.calenIcon {
  cursor: pointer;
  width: 30%;
}

.noMovie {
  text-align: center;
}

.showingList {
  background-color: rgba(51, 51, 51, 0.39);
  margin-bottom: 2px;
}
.bookButton {
  margin-right: 20px;
  cursor: pointer;
  margin-left: 5px;
}



button {
   background-color: #131313 ;
  border: 1px solid #131313 ;
  text-align: center;
  border-radius: 5px;
  color: rgb(238, 220, 192);
  font-family: "Bebas Neue", cursive;
  width: 60px;
  padding-left: 9px;
  padding-top: 2px;
  margin-top: 7px;
  margin-bottom: 7px;
  font-size: 20px;
  transition: 200ms;
}
button:active,
button:disabled {
  background-color: rgb(46, 46, 46);
}

.grow:hover {
  -webkit-transform: scale(1.2);
  -ms-transform: scale(1.2);
  transform: scale(1.2);
    background-color: #6e1020;

}
button:disabled {
  cursor: default;
}

h3 {
  color: rgb(206, 191, 168);
  font-size: 17px;
  /* font-weight: lighter; */
}
.anotherFormattingDiv {
  display: flex;
  align-items: center;
}
.noTopMargin > span {
  padding: 0 18px 0;
}
#draggable-container {
  width: 0%;
  position: absolute;
  z-index: 9;
}
#draggable-header {
  width: 0%;
  z-index: 10;
}
.popup {
  position: relative;
  display: inline-block;
  cursor: pointer;
}
.popup .popuptext {
  visibility: hidden;
  width: 160px;
  background-color: rgb(148, 11, 11);
  color: #fff;
  text-align: center;
  border-radius: 6px;
  padding: 8px 0;
  position: absolute;
  z-index: 1;
  bottom: 125%;
  left: 50%;
  margin-left: -80px;
}

.popup .show {
  visibility: visible;
  -webkit-animation: fadeIn 1s;
  animation: fadeIn 1s;
}
@-webkit-keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>