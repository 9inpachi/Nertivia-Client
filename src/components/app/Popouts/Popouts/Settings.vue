<template>
  <div class="settings-darken-background settings-popout">
    <div class="settings-box">
      <div class="tabs">
        <div
          class="tab"
          v-for="tab in tabsFiltered"
          :key="tab.index"
          :class="{ selected: currentTab === tab.index }"
          @click="currentTab = tab.index"
        >
          <div class="material-icons">{{ tab.icon }}</div>
          <div class="tab-name">{{ tab.name }}</div>
        </div>
        <div class="tab warn" @click="logout">
          <div class="material-icons">exit_to_app</div>
          <div class="tab-name">Logout</div>
        </div>
      </div>
      <div class="panel">
        <div class="title">
          <div class="material-icons">{{ tabs[currentTab].icon }}</div>
          <div class="in-title">{{ tabs[currentTab].tabName }}</div>
          <div class="close-button" @click="close">
            <div class="material-icons">close</div>
          </div>
        </div>
        <component :is="tabs[currentTab].component"></component>
      </div>
    </div>
  </div>
</template>

<script>
import { isMobile } from "@/utils/Mobile";
import isElectron from "@/utils/ElectronJS/isElectron";

const MyProfile = () => import("./SettingsPanels/MyProfile.vue");
const ManageEmojis = () => import("./SettingsPanels/ManageEmojis.vue");
const MessageDesign = () => import("./SettingsPanels/MessageDesign.vue");
const MyThemes = () => import("./SettingsPanels/MyThemes.vue");
const Notifications = () => import("./SettingsPanels/Notifications.vue");
const AppSettings = () => import("./SettingsPanels/appSettings");
const DebugSettings = () => import("./SettingsPanels/DebugSettings");

export default {
  components: {
    MyProfile,
    ManageEmojis,
    MessageDesign,
    MyThemes,
    Notifications,
    AppSettings,
    DebugSettings
  },
  data() {
    return {
      currentTab: 0,
      tabs: [
        {
          name: "My Profile",
          tabName: "My Profile",
          icon: "account_circle",
          component: "my-profile",
          index: 0
        },
        {
          name: "Message Design",
          tabName: "Message Design",
          icon: "palette",
          component: "message-design",
          index: 1
        },
        {
          name: "My Themes",
          tabName: "My Themes BETA",
          icon: "palette",
          component: "my-themes",
          index: 2
        },
        {
          name: "Manage Emojis",
          tabName: "Manage Emojis",
          icon: "face",
          component: "manage-emojis",
          index: 3
        },
        {
          name: "Notifications",
          tabName: "Notifications",
          icon: "message",
          component: "notifications",
          hidden: isMobile(),
          index: 4
        },
        {
          name: "App Settings",
          tabName: "App Settings",
          icon: "desktop_windows",
          component: "app-settings",
          hidden: !isElectron,
          index: 5
        },
        {
          name: "Debug",
          tabName: "Debug",
          icon: "bug_report",
          component: "debug-settings",
          index: 6
        }
      ]
    };
  },
  methods: {
    logout() {
      this.$store.dispatch("logout");
      setTimeout(() => {
        isElectron
          ? (window.location.href = "/login")
          : (window.location.href = "/");
      }, 1000);
    },
    close() {
      this.$store.dispatch("setPopoutVisibility", {
        name: "settings",
        visibility: false
      });
    },
    keyDownEvent(event) {
      if (event.keyCode !== 27) return; // 27 = escape
      this.close();
    }
  },
  created() {
    document.addEventListener("keydown", this.keyDownEvent);
  },
  destroyed() {
    document.removeEventListener("keydown", this.keyDownEvent);
  },
  computed: {
    tabsFiltered() {
      return this.tabs.filter(t => t.hidden !== true);
    }
  }
};
</script>

<style scoped>
.settings-darken-background {
  position: absolute;
  background: rgba(0, 0, 0, 0.541);
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 99999999;
  display: flex;
  color: white;
}
.settings-box {
  height: 630px;
  display: flex;
  margin: auto;
  overflow: hidden;
  box-shadow: 0px 0px 20px 5px #151515bd;
  background: linear-gradient(
    to bottom,
    rgba(0, 87, 153, 0.8) 0,
    rgba(0, 118, 209, 0.8)
  );
  border-radius: 4px;
  backdrop-filter: blur(5px);
}
.tabs {
  height: 100%;
  width: 200px;
  min-width: 150px;
  overflow-y: auto;
  overflow-x: hidden;
  flex-shrink: 0;
  position: relative;
  background: #00000033;
}
.panel {
  display: flex;
  flex-direction: column;
  height: 100%;
  width: 600px;
  overflow: hidden;
}
.tab {
  display: flex;
  padding: 10px;
  cursor: pointer;
  user-select: none;
  transition: 0.3s;
  align-items: center;
}
.tab-name {
  margin-left: 10px;
}

.tab:hover {
  background: rgba(0, 0, 0, 0.4);
}

.tab.selected {
  background: rgba(0, 0, 0, 0.4);
}

.tab.warn {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  color: #f54949;
}

.title {
  display: flex;
  padding: 10px;
  font-size: 20px;
  background: rgba(0, 0, 0, 0.4);
}
.title .material-icons {
  font-size: 30px;
}
.title div {
  margin: auto;
  margin-left: 5px;
  margin-right: 5px;
}
.in-title {
  flex: 1;
}
.close-button {
  display: flex;
  border-radius: 50%;
  padding: 5px;
  cursor: pointer;
  user-select: none;
  transition: 0.3s;
}
.close-button:hover {
  background: #03181f;
}
.close-button .material-icons {
  margin: auto;
  font-size: 30px;
}

/* ------- SCROLL BAR -------*/
/* width */
.tabs::-webkit-scrollbar {
  height: 3px;
}

/* Track */
.tabs::-webkit-scrollbar-track {
  background: #8080806b;
}

/* Handle */
.tabs::-webkit-scrollbar-thumb {
  background: #f5f5f559;
}

/* Handle on hover */
.tabs::-webkit-scrollbar-thumb:hover {
  background: #f5f5f59e;
}

@media (max-width: 815px) {
  .settings-box {
    width: 100%;
  }
}
@media (max-height: 609px) {
  .settings-box {
    height: 100%;
  }
}
@media (max-width: 550px) {
  .settings-box {
    flex-direction: column;
    height: 100%;
    border-radius: 0;
  }
  .tabs {
    height: 50px;
    width: 100%;
    display: flex;
    overflow: hidden;
    overflow-x: auto;
    flex-shrink: 0;
  }
  .tab.warn {
    position: relative;
  }
  .tab {
    flex-shrink: 0;
  }
  .panel {
    width: 100%;
  }
}
</style>
