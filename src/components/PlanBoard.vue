<template>
  <div class="plan-board">
    <div class="plan-board-header">
      <p
        :class="tabsClassess[index]"
        v-for="(tab, index) in tabs"
        :key="tab"
        @click="setActiveTab(index)"
      >
        {{ tab }}
      </p>
    </div>

    <div class="plan-board-body">
      <div class="plan-board-body-items" v-if="sortedItems.length">
        <div
          v-for="(item, index) in sortedItems"
          :key="item.name"
          :class="itemsClassess[index]"
          @click="handleItemCheck(item)"
        >
          <p class="plan-board-body-items-item-name">
            {{ item.name }}
          </p>

          <div class="plan-board-body-items-item-check" />
        </div>

        <div v-if="removeMode" class="plan-board-body-items-edit">
          <p class="plan-board-body-items-edit-edit" @click="exitRemoveMode">
            Cancel
          </p>
          <p class="plan-board-body-items-edit-remove" @click="removeItems">
            Apply
          </p>
        </div>

        <div v-else class="plan-board-body-items-edit">
          <p class="plan-board-body-items-edit-remove" @click="enterRemoveMode">
            Remove
          </p>
        </div>
      </div>

      <div class="plan-board-body-noitem" v-else>
        {{ noItemText }}
      </div>

      <div class="plan-board-body-input">
        <input
          @keyup="handleNewTaskText($event)"
          :value="newTaskText"
          type="text"
        />
        <button @click="addItem">Add</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";

type Props = {
  tabs: string[];
  item: { name: string; checked: boolean; willRemove: boolean };
  items: Props["item"][];
};

export default Vue.extend({
  name: "PlanBoard",
  props: {
    tabs: {
      type: Array as PropType<Props["tabs"]>,
      default: () => ["All", "To do", "Done"],
      required: true,
      validator(tabs: Props["tabs"]) {
        return !tabs.some((tab) => typeof tab !== "string");
      },
    },
  },
  data: () => ({
    activeTab: 0 as number,
    items: [
      { name: "Example task", checked: false, willRemove: false },
      { name: "Example done task", checked: true, willRemove: false },
      { name: "You can remove this task", checked: false, willRemove: false },
    ] as Props["items"],
    newTaskText: "" as string,
    removeMode: false as boolean,
  }),
  computed: {
    noItemText: function (): string {
      return (
        {
          1: "No items to do",
          2: "No items done",
        }[this.activeTab] || "No items"
      );
    },
    tabsClassess: function (): string[] {
      return this.tabs.map((_, index) =>
        index !== this.activeTab
          ? "plan-board-header-tab"
          : "plan-board-header-tab plan-board-header-tab-active"
      );
    },
    itemsClassess: function (): string[][] {
      return this.sortedItems.map((item) => [
        "plan-board-body-items-item",
        this.removeMode && item.willRemove
          ? "plan-board-body-items-item-remove"
          : !this.removeMode && item.checked
          ? "plan-board-body-items-item-checked"
          : "",
      ]);
    },
    sortedItems: function (): Props["items"] {
      return this.items.length
        ? [...this.items]
            .sort((x, y) =>
              this.removeMode
                ? x.willRemove === y.willRemove
                  ? 0
                  : x.willRemove
                  ? -1
                  : 1
                : x.checked === y.checked
                ? 0
                : x.checked
                ? -1
                : 1
            )
            .filter((item) =>
              this.activeTab === 2
                ? item.checked
                : this.activeTab === 1
                ? !item.checked
                : true
            )
        : [];
    },
  },
  methods: {
    handleNewTaskText: function (event: KeyboardEvent) {
      const target = event.target as HTMLInputElement;
      const enter = event.key === "Enter" || event.keyCode === 13;

      this.newTaskText = target.value;

      if (enter) {
        this.addItem();
      }
    },
    setActiveTab: function (tab: number) {
      this.activeTab = tab;
    },
    handleItemCheck: function (item: Props["item"]) {
      if (this.removeMode) {
        item.willRemove = !item.willRemove;
      } else {
        item.checked = !item.checked;
      }
    },
    addItem: function () {
      const object = {
        name: this.newTaskText,
        checked: false,
        willRemove: false,
      };

      this.items.push(object);

      this.newTaskText = "";
    },
    removeItems: function () {
      this.items = this.sortedItems.filter((item) => !item.willRemove);

      this.removeMode = false;
    },
    enterRemoveMode: function () {
      this.removeMode = true;
    },
    exitRemoveMode: function () {
      this.sortedItems.map((item) => (item.willRemove = false));

      this.removeMode = false;
    },
  },
});
</script>

<style scoped lang="scss">
.plan-board {
  border: 1px solid #c4c4c4;
  border-radius: 4px;
  background-color: #fff;
  overflow: hidden;

  &-header {
    display: flex;
    background: #fafafa;
    border-bottom: 1px solid #c4c4c4;

    &-tab {
      cursor: pointer;
      color: #41b883;
      padding: 8px;
      border-right: 1px solid #c4c4c4;
      background: #f4f4f4;
      font-weight: 600;
      flex: 1;

      &:hover {
        background: #eaeaea;
      }

      &-active {
        background-color: #fff;
        box-shadow: 0px 1px #fff;

        &:hover {
          background: #fff;
        }
      }
    }
  }

  &-body {
    &-noitem {
      padding: 8px;
      color: #35495e;
    }

    &-items {
      &-item {
        $item: &;
        display: flex;
        border: 1px solid #c4c4c4;
        border-radius: 4px;
        padding: 8px;
        margin: 8px;
        background: #fafafa;
        cursor: pointer;

        &-checked {
          text-decoration: line-through;
          background-color: #f4f4f4;

          #{$item}-check {
            background-color: #41b883;
          }
        }

        &-remove {
          text-decoration: line-through;
          background-color: #f4f4f4;

          #{$item}-check {
            background-color: #e53935;
          }
        }

        &-name {
          flex: 1;
          text-align: left;
        }

        &-check {
          height: 20px;
          width: 20px;
          border: 1px solid #c4c4c4;
          border-radius: 4px;
          background-color: #35495e;
        }
      }

      &-edit {
        display: flex;
        border-top: 1px solid #c4c4c4;
        cursor: pointer;

        &-edit {
          flex: 1;
          padding: 8px;
          border-right: 1px solid #c4c4c4;
          background-color: #f4f4f4;
        }

        &-remove {
          flex: 1;
          padding: 8px;
          background-color: #f4f4f4;
        }
      }
    }

    &-input {
      display: flex;
      border-top: 1px solid #c4c4c4;
      padding: 8px;
      background-color: #fafafa;

      input {
        flex: 1;
        border: 1px solid #c4c4c4;
        border-radius: 4px;
        padding: 8px;
        border-top-right-radius: 0px;
        border-bottom-right-radius: 0px;

        &:active,
        &:focus {
          outline: none;
        }
      }

      button {
        background-color: #41b883;
        border: none;
        color: #35495e;
        font-weight: 600;
        padding: 0px 16px;
        cursor: pointer;
        border-top-right-radius: 4px;
        border-bottom-right-radius: 4px;
      }
    }
  }

  p {
    margin: 0;
  }
}
</style>
