<template>
  <div class="app-container">
    <header>
      <figure class="desktop-bg">
        <img v-show="!isDark" src="@/assets/bg-desktop-light.jpg" alt="" />
        <img v-show="isDark" src="@/assets/bg-desktop-dark.jpg" />
      </figure>
    </header>
    <div class="wrapper">
      <main>
        <div class="section-one">
          <h1>TODO</h1>
          <figure @click="changeTheme()">
            <img v-show="!isDark" src="@/assets/icon-moon.svg" alt="" />
            <img v-show="isDark" src="@/assets/icon-sun.svg" alt="" />
          </figure>
        </div>
        <div class="section-two" :class="{ 'section-two-dark': isDark }">
          <form action="" v-on:submit.prevent="addTask()">
            <div class="input-container">
              <button :class="{ button: isDark }"></button>
              <input
                class="text-input"
                :class="{ 'text-input-dark': isDark }"
                type="text"
                v-model.trim="task"
                placeholder="Create a new todo..."
              />
            </div>
          </form>

          <div class="list-container">
            <ul>
              <div @drop="onDrop($event)" @dragover.prevent @dragenter.prevent>
                <li
                  class="round"
                  :style="{ borderTop: isDark ? '1px solid  #393A4B' : '' }"
                  v-for="(task, index) in taskArray"
                  :key="task"
                  draggable="true"
                  @dragstart="startDrag($event, task)"
                  @mouseover="getElement($event)"
                  @mouseleave="hideCrossIcon"
                >
                  <label :for="task" class="container">
                    <input
                      class="check-box"
                      type="checkbox"
                      :id="task"
                      :value="task"
                      v-model="checkedTask"
                    />
                    <span
                      class="checkmark"
                      :class="{ 'checkmark-dark': isDark }"
                    ></span>
                  </label>
                  <p
                    class="task"
                    :class="{ checked: isChecked(task) }"
                    :style="{ color: isDark ? '#C8CBE7' : '' }"
                  >
                    {{ task }}
                  </p>
                  <figure
                    class="cross"
                    :class="{ 'icon-cross': displayCrossIcon(task) }"
                    @click="removeTask(index)"
                  >
                    <img src="@/assets/icon-cross.svg" alt="" />
                  </figure>
                </li>
              </div>
              <li
                class="menu"
                v-if="taskArray.length > 0"
                :style="{ borderTop: isDark ? '1px solid  #393A4B' : '' }"
              >
                <div class="tabs">
                  <p>{{ itemsLeft }} items left</p>
                </div>
                <div class="routes">
                  <p
                    class="tabs middle"
                    :class="{ active: activeTab === 'all' ? true : false }"
                    @click="all($event)"
                  >
                    all
                  </p>
                  <p
                    class="tabs middle"
                    :class="{ active: activeTab === 'active' ? true : false }"
                    @click="activeList($event)"
                  >
                    active
                  </p>
                  <p class="tabs" @click="completedList($event)">completed</p>
                </div>
                <div>
                  <p class="tabs" @click="clearCompleted()">clear completed</p>
                </div>
              </li>
            </ul>
          </div>
        </div>
        <div class="text-container" v-if="taskArray.length > 1">
          <p>Drag and drop to reorder list</p>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      hover: null,
      task: null,
      taskArray: [],
      allTask: [],
      checkedTask: [],
      isDark: false,
      activeTab: "all",
    };
  },

  methods: {
    changeTheme() {
      this.isDark = !this.isDark;
      let rootElement = document.body;
      this.isDark
        ? (rootElement.style.backgroundColor = "#000")
        : (rootElement.style.backgroundColor = "#fff");
    },
    startDrag(evt, task) {
      evt.dataTransfer.dropEffect = "move";
      evt.dataTransfer.effectAllowed = "move";
      evt.dataTransfer.setData("itemID", task);
    },
    onDrop(evt) {
      const task = evt.dataTransfer.getData("itemID");
      this.taskArray.push(task);
      // const item = this.items.find((item) => item.id == itemID)
      // item.list = list
    },

    addTask() {
      if (!this.task) {
        return false;
      }
      this.taskArray.push(this.task);
      this.allTask.push(this.task);
      this.task = "";
    },

    activeList(event) {
      const text = event.target.innerText;
      this.activeTab = text;
      if (this.checkedTask.length > 0) {
        this.taskArray = this.allTask
          .filter((_task) => !this.checkedTask.includes(_task))
          .slice();
      } else {
        this.taskArray = this.allTask;
      }
    },

    completedList(event) {
      const text = event.target.innerText;
      this.activeTab = text;
      this.checkedTask.length > 0
        ? (this.taskArray = this.checkedTask.slice())
        : (this.taskArray = this.allTask.slice());
    },

    all(event) {
      this.taskArray = this.allTask.slice();
      const text = event.target.innerText;
      this.activeTab = text;
    },

    clearCompleted() {
      this.taskArray = this.allTask
        .filter((_task) => !this.checkedTask.includes(_task))
        .slice();
      this.allTask = this.taskArray.slice();
      this.checkedTask = [];
    },

    getElement(event) {
      const text = event.target.innerText;
      this.hover = text;
    },

    hideCrossIcon() {
      this.hover = null;
    },

    removeTask(index) {
      this.taskArray.splice(index, 1);
      this.allTask.splice(index, 1);
    },
  },

  computed: {
    itemsLeft() {
      let all = this.allTask.length;
      let done = this.checkedTask.length;
      let remaining = all - done;
      return remaining;
    },

    isChecked() {
      return (task) => {
        return this.checkedTask.includes(task);
      };
    },

    displayCrossIcon() {
      return (task) => {
        return this.hover === task ? true : false;
      };
    },
  },
};
</script>

<style scoped>
header,
header figure {
  width: 100%;
}

header figure img {
  width: 100%;
  object-fit: cover;
}

main {
  width: 33.75rem;
  margin: 0 auto;
  position: absolute;
  top: 70px;
  left: 450px;
}

h1 {
  font-weight: 700;
  font-size: 2.5rem;
  line-height: 2.5rem;
  letter-spacing: 0.9375rem;
  color: #fff;
}

li {
  list-style: none;
  padding: 1.25rem 1.5rem;
  border-top: 1px solid #e3e4f1;
}

li figure {
  float: right;
  padding: 4px;
}

input {
  outline: none;
  border: none;
}

input[type="text"],
.text-input::placeholder {
  font-family: "Josefin Sans";
  font-weight: 400;
  font-size: 18px;
  line-height: 18px;
  /* identical to box height */
  letter-spacing: -0.25px;
  color: #9495a5;
}

.text-input-dark {
  background: #25273d;
}

input[type="text"] {
  color: #494c6b;
}

.text-input-dark[type="text"] {
  color: #c8cbe7;
}

button {
  width: 24px;
  height: 24px;
  background-color: #fff;
  border-radius: 50%;
  border: 1px solid #e3e4f1;
  margin-right: 24px;
}

.button {
  background: #25273d;
  border: 1px solid #393a4b;
}

.section-one {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.section-two div {
  width: 33.75rem;
  background-color: #fff;
}

.section-two-dark div {
  background: #25273d;
}

.list-container {
  margin-top: 1.5rem;
  border-radius: 5px;
  filter: drop-shadow(0px 4px 4px rgba(0, 0, 0, 0.25));
}

.input-container {
  margin-top: 2.5rem;
  padding: 1.25rem 1.5rem;
  border-radius: 5px;
  display: flex;
  align-items: center;
}

.menu {
  display: flex;
}

.routes {
  display: flex;
  justify-content: space-between;
  margin-left: 76px;
  margin-right: 3.5rem;
}

.routes p {
  font-weight: 700;
}

.cross {
  display: none;
}

.icon-cross {
  display: block;
  cursor: pointer;
}

.task {
  display: inline-block;
  margin-left: 48px;
  color: #494c6b;
}

.checked {
  text-decoration: line-through;
  color: #d1d2da;
}

.tabs {
  font-size: 14px;
  line-height: 14px;
  /* identical to box height */
  letter-spacing: -0.194444px;
  cursor: pointer;
  color: #9495a5;
}

.active,
.tabs:hover {
  color: #3a7cfd;
}

.middle {
  margin-right: 1.1875rem;
}

.text-container {
  margin-top: 49px;
  text-align: center;
}

.text-container p {
  font-weight: 400;
  font-size: 14px;
  line-height: 14px;
  text-align: center;
  letter-spacing: -0.194444px;
  color: #9495a5;
}

/* The container */
.container {
  display: block;
  position: relative;
  top: -4px;
  left: 0;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* Hide the browser's default checkbox */
.container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 25px;
  width: 25px;
  border: 1px solid #e3e4f1;
  border-radius: 50%;
  background-color: #fff;
  display: block;
}

.checkmark-dark {
  background: #25273d;
  border: 1px solid #393a4b;
}

/* On mouse-over, add a white color */
.container:hover input ~ .checkmark {
  background-color: #fff;
}

/* When the checkbox is checked, add the gradient colour */
.container input:checked ~ .checkmark {
  background: linear-gradient(
    310deg,
    hsl(280, 87%, 65%) 0%,
    hsl(192, 100%, 67%) 99%
  );
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.container input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container .checkmark:after {
  left: 7px;
  top: 4px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

@media screen and (max-width: 600px) {
  header figure img {
    width: 100%;
    height: 300px;
    object-fit: cover;
  }

  main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 0 auto;
    width: 100%;
    padding: 0 24px;
    left: 0px;
  }

  li {
    padding: 14px 20px;
  }

  .section-one {
    width: 20.4375rem;
  }

  .cross {
    display: block;
  }

  .icon-cross {
    display: none;
  }

  .section-two {
    width: 20.4375rem;
  }

  .section-two div {
    width: 100%;
    /* position: relative; */
  }

  .menu {
    justify-content: space-between;
    position: relative;
  }

  .routes {
    padding: 15px 80px;
    position: absolute;
    top: 50px;
    left: 0;
    width: 20.4375rem;
    margin-left: 0;
    margin-right: 0;
  }

  .text-container {
    margin-top: 89px;
  }
}
</style>
