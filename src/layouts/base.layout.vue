<script lang="ts" setup>
import { NIcon, useThemeVars, type MenuGroupOption } from 'naive-ui';
import { computed, h } from 'vue';
import { RouterLink, useRoute } from 'vue-router';
import { Heart, Menu2, Home2 } from '@vicons/tabler';
import { toolsByCategory } from '@/tools';
import { useStyleStore } from '@/stores/style.store';
import { config } from '@/config';
import MenuIconItem from '@/components/MenuIconItem.vue';
import type { Tool } from '@/tools/tools.types';
import { useToolStore } from '@/tools/tools.store';
import { useTracker } from '@/modules/tracker/tracker.services';
import SearchBar from '../components/SearchBar.vue';
import HeroGradient from '../assets/hero-gradient.svg?component';
import MenuLayout from '../components/MenuLayout.vue';
import NavbarButtons from '../components/NavbarButtons.vue';

const themeVars = useThemeVars();
const route = useRoute();
const styleStore = useStyleStore();
const version = config.app.version;
const commitSha = config.app.lastCommitSha.slice(0, 7);

const makeLabel = (tool: Tool) => () => h(RouterLink, { to: tool.path }, { default: () => tool.name });
const makeIcon = (tool: Tool) => () => h(MenuIconItem, { tool });

const { tracker } = useTracker();

const toolStore = useToolStore();

const menuOptions = computed<MenuGroupOption[]>(() =>
  [
    ...(toolStore.favoriteTools.length > 0
      ? [{ name: 'Your favorite tools', components: toolStore.favoriteTools }]
      : []),
    ...toolsByCategory,
  ].map((category) => ({
    label: category.name,
    key: category.name,
    type: 'group',
    children: category.components.map((tool) => ({
      label: makeLabel(tool),
      icon: makeIcon(tool),
      key: tool.name,
    })),
  })),
);
</script>

<template>
  <menu-layout class="menu-layout" :class="{ isSmallScreen: styleStore.isSmallScreen }">
    <template #sider>
      <router-link to="/" class="hero-wrapper">
        <hero-gradient class="gradient" />
        <div class="text-wrapper">
          <div class="title">IT - TOOLS</div>
          <div class="divider" />
          <div class="subtitle">Handy tools for developers</div>
        </div>
      </router-link>

      <div class="sider-content">
        <n-space v-if="styleStore.isSmallScreen" justify="center">
          <navbar-buttons />
        </n-space>

        <n-menu
          class="menu"
          :value="(route.name as string)"
          :collapsed-width="64"
          :collapsed-icon-size="22"
          :options="menuOptions"
          :indent="20"
        />

        <div class="footer">
          <div>
            IT-Tools

            <n-button
              text
              tag="a"
              target="_blank"
              rel="noopener"
              type="primary"
              depth="3"
              :href="`https://github.com/CorentinTh/it-tools/tree/v${version}`"
            >
              v{{ version }}
            </n-button>

            <template v-if="commitSha && commitSha.length > 0">
              -
              <n-button
                text
                tag="a"
                target="_blank"
                rel="noopener"
                type="primary"
                depth="3"
                :href="`https://github.com/CorentinTh/it-tools/tree/${commitSha}`"
              >
                {{ commitSha }}
              </n-button>
            </template>
          </div>
          <div>
            © {{ new Date().getFullYear() }}
            <n-button text tag="a" target="_blank" rel="noopener" type="primary" href="https://github.com/CorentinTh">
              Corentin Thomasset
            </n-button>
          </div>
        </div>
      </div>
    </template>

    <template #content>
      <div class="navigation">
        <n-button
          :size="styleStore.isSmallScreen ? 'medium' : 'large'"
          circle
          quaternary
          aria-label="Toggle menu"
          @click="styleStore.isMenuCollapsed = !styleStore.isMenuCollapsed"
        >
          <n-icon size="25" :component="Menu2" />
        </n-button>

        <router-link to="/" #="{ navigate, href }" custom>
          <n-tooltip trigger="hover">
            <template #trigger>
              <n-button
                tag="a"
                :href="href"
                :size="styleStore.isSmallScreen ? 'medium' : 'large'"
                circle
                quaternary
                aria-label="Home"
                @click="navigate"
              >
                <n-icon size="25" :component="Home2" />
              </n-button>
            </template>
            Home
          </n-tooltip>
        </router-link>

        <search-bar />

        <navbar-buttons v-if="!styleStore.isSmallScreen" />

        <n-tooltip trigger="hover">
          <template #trigger>
            <n-button
              round
              type="primary"
              tag="a"
              href="https://www.buymeacoffee.com/cthmsst"
              rel="noopener"
              target="_blank"
              class="support-button"
              :bordered="false"
              @click="() => tracker.trackEvent({ eventName: 'Support button clicked' })"
            >
              Buy me a coffee
              <n-icon v-if="!styleStore.isSmallScreen" :component="Heart" style="margin-left: 5px" />
            </n-button>
          </template>
          ❤ Support IT Tools development !
        </n-tooltip>
      </div>
      <slot />
    </template>
  </menu-layout>
</template>

<style lang="less" scoped>
// ::v-deep(.n-layout-scroll-container) {
//     @percent: 4%;
//     @position: 25px;
//     @size: 50px;
//     @color: #eeeeee25;
//     background-image: radial-gradient(@color @percent, transparent @percent),
//         radial-gradient(@color @percent, transparent @percent);
//     background-position: 0 0, @position @position;
//     background-size: @size @size;
// }

.support-button {
  background: rgb(37, 99, 108);
  background: linear-gradient(48deg, rgba(37, 99, 108, 1) 0%, rgba(59, 149, 111, 1) 60%, rgba(20, 160, 88, 1) 100%);
  color: #fff;
  transition: all ease 0.2s;

  &:hover {
    color: #fff;
    padding-left: 30px;
    padding-right: 30px;
  }
}

.footer {
  text-align: center;
  color: #838587;
  margin-top: 20px;
  padding: 20px 0;
}

.sider-content {
  padding-top: 160px;
  padding-bottom: 200px;
}

.hero-wrapper {
  position: absolute;
  display: block;
  left: 0;
  width: 100%;
  z-index: 10;
  overflow: hidden;

  .gradient {
    margin-top: -65px;
  }

  .text-wrapper {
    position: absolute;
    left: 0;
    width: 100%;
    text-align: center;
    top: 16px;
    color: #fff;

    .title {
      font-size: 25px;
      font-weight: 600;
    }

    .divider {
      width: 50px;
      height: 2px;
      border-radius: 4px;
      background-color: v-bind('themeVars.primaryColor');
      margin: 0 auto 5px;
    }

    .subtitle {
      font-size: 16px;
    }
  }
}

// ::v-deep(.n-menu-item-content-header) {
//   overflow: visible !important;
//   // overflow-x: hidden !important;
// }

.navigation {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: row;

  & > *:not(:last-child) {
    margin-right: 5px;
  }

  .search-bar {
    // width: 100%;
    flex-grow: 1;
  }
}
</style>
