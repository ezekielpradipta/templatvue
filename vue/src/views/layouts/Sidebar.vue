<script setup>
import { useStore } from "vuex";
import { ref, onMounted, onBeforeUnmount } from "vue";
import { useRoute } from "vue-router";
import { Disclosure, DisclosureButton, DisclosurePanel } from "@headlessui/vue";
import {
  AcademicCapIcon,
  BookOpenIcon,
  CalculatorIcon,
  DocumentIcon,
  HomeIcon,
  PlusIcon,
  UserGroupIcon,
} from "@heroicons/vue/24/outline";
import jwt_decode from "jwt-decode";
const getToken = localStorage.getItem("token");
function getRole() {
  const token = jwt_decode(getToken);
  const userRole = token.user.roles[0].name;
  if (userRole === "user") {
    const navigation = [
      {
        name: "Dashboard",
        to: { name: "User" },
        icon: HomeIcon,
        namamenu: "dashboardA",
      },
      {
        name: "UserPage1",
        to: { name: "UserPage" },
        icon: HomeIcon,
        namamenu: "userpage1",
      },
    ];
    return navigation;
  } else if (userRole === "admin") {
    const navigation = [
      {
        name: "Dashboard",
        to: { name: "Admin" },
        icon: HomeIcon,
        namamenu: "dashboardA",
      },
      {
        name: "AdminPage1",
        to: { name: "AdminPage" },
        icon: HomeIcon,
        namamenu: "adminpage1",
      },
    ];
    return navigation;
  }
}
// const navigation = [
//   {
//     name: "Dashboard",
//     to: { name: "Dashboard" },
//     icon: HomeIcon,
//     namamenu: "dashboard",
//   },
//   {
//     name: "Dosen",
//     to: { name: "Dosen" },
//     icon: UserGroupIcon,
//     namamenu: "dosen",
//   },
//   {
//     name: "Mahasiswa",
//     to: { name: "Mahasiswa" },
//     icon: AcademicCapIcon,
//     namamenu: "mahasiswa",
//   },
//   {
//     name: "Mata Kuliah",
//     to: { name: "MataKuliah" },
//     icon: BookOpenIcon,
//     namamenu: "matakuliah",
//   },
//   {
//     name: "Skala",
//     to: { name: "Skala" },
//     icon: PlusIcon,
//     namamenu: "skala",
//   },
//   {
//     name: "Data Lulus DO",
//     to: { name: "MahasiswaLulus" },
//     icon: AcademicCapIcon,
//     namamenu: "mahasiswalulus",
//   },
//   {
//     name: "Hitung AKM",
//     to: { name: "AKM" },
//     icon: CalculatorIcon,
//     namamenu: "hitungakm",
//   },
//   {
//     name: "Log ",
//     to: { name: "Log" },
//     icon: DocumentIcon,
//   },
// ];

let store = useStore();
const route = useRoute();

let selectedParentMenu = ref("");
let isMenuOver = ref(false);

onMounted(() => {
  toggleSelectedParentMenu();
  document.addEventListener("click", returnSelectedParentMenu);
});

// beforeDestroy
onBeforeUnmount(() => {
  document.removeEventListener("click", returnSelectedParentMenu);
});

const returnSelectedParentMenu = () => {
  if (!isMenuOver.value) {
    toggleSelectedParentMenu();
  }
};
const removeOverlay = () => {
  store.dispatch(
    "largeSidebar/changeSecondarySidebarPropertiesViaMenuItem",
    false
  );
  // if (window.innerWidth <= 1200) {

  //   this.changeSidebarProperties()
  //}
  toggleSelectedParentMenu();
};

let toggleSelectedParentMenu = () => {
  let currentParentUrl = route.path.split("/").filter((x) => x !== "")[0];

  if (currentParentUrl !== undefined || currentParentUrl !== null) {
    selectedParentMenu.value = currentParentUrl.toLowerCase();
  } else {
    selectedParentMenu.value = "";
  }
};
let toggleSubMenu = (e) => {
  let hasSubmenu = e.target.dataset.submenu;
  let parent = e.target.dataset.item;

  if (hasSubmenu) {
    selectedParentMenu.value = parent;
    store.dispatch(
      "largeSidebar/changeSecondarySidebarPropertiesViaMenuItem",
      true
    );
  } else {
    selectedParentMenu.value = parent;
    store.dispatch(
      "largeSidebar/changeSecondarySidebarPropertiesViaMenuItem",
      false
    );
  }
};
</script>
<template>
  <div
    class="side-content-wrap"
    @mouseenter="isMenuOver = true"
    @mouseleave="isMenuOver = false"
    @touchstart="isMenuOver = true"
  >
    <div class="side-content-wrap">
      <div
        :class="
          store.state.largeSidebar.sidebarToggleProperties.isSideNavOpen ===
          true
            ? 'open'
            : ''
        "
        class="sidebar-left"
      >
        <perfect-scrollbar>
          <ul class="navigation-left">
            <li
              @mouseenter="toggleSubMenu"
              v-for="item in getRole()"
              :key="item.name"
              class="nav-item"
              :data-item="item.namamenu"
              :class="selectedParentMenu == item.namamenu ? 'active' : ''"
              :data-submenu="item.submenu"
            >
              <router-link
                :to="item.to"
                class="nav-item-hold"
                :class="[this.$route.name === item.to.name ? '' : ' ']"
              >
                <i class="text-3xl">
                  <component :is="item.icon" class="h-6 w-6 inline" />
                </i>
                <p>{{ item.name }}</p>
              </router-link>
            </li>
            <!--
            <router-link
              @mouseenter="toggleSubMenu"
              v-for="item in navigation"
              :key="item.name"
              :to="item.to"
              class="nav-item"
              active-class=""
              :class="[
                this.$route.name === item.to.name ? '' : ' ',
                selectedParentMenu == item.namamenu ? 'active' : '',
              ]"
              :data-item="item.namamenu"
              :data-submenu="item.submenu"
            >
              <div class="nav-item-hold">
                <i class="text-3xl">
                  <component :is="item.icon" class="h-6 w-6 inline" />
                </i>
                <p>{{ item.name }}</p>
              </div>
            </router-link>
            -->
          </ul>
        </perfect-scrollbar>
      </div>

      <div
        :class="{
          open: store.state.largeSidebar.sidebarToggleProperties
            .isSecondarySideNavOpen,
        }"
        class="sidebar-left-secondary shadow"
      >
        <!-- <ul
          v-for="item in submenulist"
          :key="item.name"
          class="mb-4 childNav"
          data-parent="dashboards"
          :class="selectedParentMenu == item.namamenu ? 'block' : 'hidden'"
        >
          <li>
            <router-link :to="item.to">
              <i class="nav-icon mr-2">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-6 w-6"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M4 8V4m0 0h4M4 4l5 5m11-1V4m0 0h-4m4 0l-5 5M4 16v4m0 0h4m-4 0l5-5m11 5l-5-5m5 5v-4m0 4h-4"
                  />
                </svg>
              </i>
              <p>{{ item.name }}</p>
            </router-link>
          </li>
        </ul> -->
      </div>
    </div>

    <div
      @click="removeOverlay()"
      class="sidebar-overlay"
      :class="{
        open: store.state.largeSidebar.sidebarToggleProperties
          .isSecondarySideNavOpen,
      }"
    ></div>
  </div>
</template>

<style lang="scss" scoped>
.submenuLi {
  &:hover {
    .submenuli-icon {
      color: #8b5cf6;
    }
  }
  .submenuli-icon {
    color: #9ca3af;
  }
}
.submneu-nested-link {
  padding: 0 !important;
  color: #000 !important;
  &:hover {
    background-color: transparent !important;
    color: #8b5cf6 !important;
  }
}
.side-content-wrap {
  .sidebar-left {
    position: fixed;
    top: 80px;
    left: -120px;
    width: 120px;

    background: #fff;
    box-shadow: 0 4px 20px 1px rgba(0, 0, 0, 0.06),
      0 1px 4px rgba(0, 0, 0, 0.08);
    z-index: 90;
    transition: all 0.24s ease-in-out;
    &.open {
      left: 0;
      transition: all 0.24s ease-in-out;
    }
    .ps {
      height: calc(100vh - 80px);
    }
    .navigation-left {
      list-style: none;
      text-align: center;
      width: 120px;
      height: 100%;
      margin: 0;
      padding: 0;
      .nav-item,
      .nav-item-single {
        position: relative;
        display: block;
        width: 100%;
        overflow: hidden;
        cursor: pointer;
        &:hover {
          .nav-item-hold {
            @apply text-purple-500;
          }
          &:after {
            content: "";
            position: absolute;
            width: 30px;
            height: 30px;
            bottom: -15px;
            right: -15px;
            transform: rotate(45deg);
            @apply bg-purple-500;
          }
        }

        &.active {
          @apply text-purple-500;

          &:after {
            content: "";
            position: absolute;
            width: 30px;
            height: 30px;
            bottom: -15px;
            right: -15px;
            transform: rotate(45deg);
            @apply bg-purple-500;
          }
        }
        border-bottom: 1px solid #dee2e6;
        .nav-item-hold {
          display: block;
          width: 100%;
          padding: 26px 0;
          span.material-icons {
            font-size: 2rem;
          }
        }
      }
    }
  }
  .sidebar-left-secondary {
    position: fixed;
    top: 80px;
    left: calc(-220px - 20px);
    z-index: 89;
    height: calc(100vh - 80px);
    width: 220px;
    padding: 0.75rem 0;
    transition: all 0.24s ease-in-out;
    background: #fff;

    &.open {
      left: 120px;
      transition: all 0.24s ease-in-out;
    }
    ul.childNav {
      li {
        &.dropdown-sidemenu {
          display: block;
          transition: all 0.3s ease-in;
          &.open {
            a {
              .dd-arrow {
                transform: rotate(90deg);
                transition: all 0.3s ease-in;
              }
            }
            ul.submenu {
              display: block;
              max-height: 1000px;
              transition: all 0.3s ease-in;
            }
          }

          a {
            .dd-arrow {
              margin-left: auto !important;
              transition: all 0.3s ease-in;
            }
          }
        }

        // &.active {
        //     a {
        //         background-color: #f3f4f6;
        //         @apply text-purple-500;
        //     }
        // }
        a {
          text-transform: capitalize;
          display: flex;
          align-items: center;
          font-size: 13px;
          cursor: pointer;
          padding: 12px 24px;
          transition: 0.15s all ease-in;
          &:hover {
            background-color: #f3f4f6;
            @apply text-purple-500;
          }
          &.router-link-active.router-link-exact-active {
            @apply text-purple-500;
          }
        }
        ul.submenu {
          @apply bg-gray-50;
          display: none;
          max-height: 0px;
          transition: all 0.3s ease-in;

          &.open {
            display: block;
            transition: all 0.3s ease-in;
          }
          li {
            a {
              padding-left: 48px;
            }
          }
        }
      }
    }
  }
  .sidebar-overlay {
    display: none;
    position: fixed;
    width: calc(100% - 120px - 220px);
    height: calc(100vh - 80px);
    bottom: 0;
    right: 0;
    background: rgba(0, 0, 0, 0);
    z-index: 101;
    cursor: w-resize;
    &.open {
      display: block;
    }
  }
}
</style>
