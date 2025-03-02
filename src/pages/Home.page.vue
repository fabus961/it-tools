<script setup lang="ts">
import { useToolStore } from '@/tools/tools.store';
import { Heart } from '@vicons/tabler';
import { useHead } from '@vueuse/head';
import ColoredCard from '../components/ColoredCard.vue';
import ToolCard from '../components/ToolCard.vue';

const toolStore = useToolStore();

useHead({ title: 'IT Tools - Handy online tools for developers' });
</script>

<template>
  <div class="home-page">
    <div class="grid-wrapper">
      <n-grid x-gap="12" y-gap="12" cols="1 400:2 800:3 1200:4 2000:8">
        <n-gi>
          <colored-card title="You like it-tools?" :icon="Heart">
            Give us a star on
            <a
              href="https://github.com/CorentinTh/it-tools"
              rel="noopener"
              target="_blank"
              aria-label="IT-Tools' GitHub repository"
              >GitHub</a
            >
            or follow us on
            <a
              href="https://twitter.com/ittoolsdottech"
              rel="noopener"
              target="_blank"
              aria-label="IT-Tools' Twitter account"
              >Twitter</a
            >! Thank you
            <n-icon :component="Heart" />
          </colored-card>
        </n-gi>
      </n-grid>

      <transition name="height">
        <div v-if="toolStore.favoriteTools.length > 0">
          <n-h3>Your favorite tools</n-h3>
          <n-grid x-gap="12" y-gap="12" cols="1 400:2 800:3 1200:4 2000:8">
            <n-gi v-for="tool in toolStore.favoriteTools" :key="tool.name">
              <tool-card :tool="tool" />
            </n-gi>
          </n-grid>
        </div>
      </transition>

      <div v-if="toolStore.newTools.length > 0">
        <n-h3>Newest tools</n-h3>
        <n-grid x-gap="12" y-gap="12" cols="1 400:2 800:3 1200:4 2000:8">
          <n-gi v-for="tool in toolStore.newTools" :key="tool.name">
            <tool-card :tool="tool" />
          </n-gi>
        </n-grid>
      </div>

      <n-h3>All the tools</n-h3>
      <n-grid x-gap="12" y-gap="12" cols="1 400:2 800:3 1200:4 2000:8">
        <n-gi v-for="tool in toolStore.tools" :key="tool.name">
          <transition>
            <tool-card :tool="tool" />
          </transition>
        </n-gi>
      </n-grid>
    </div>
  </div>
</template>

<style scoped lang="less">
.home-page {
  padding-top: 50px;
}

.n-h3 {
  margin-bottom: 10px;
}

::v-deep(.n-grid) {
  margin-bottom: 30px;
}

.height-enter-active,
.height-leave-active {
  transition: all 0.5s ease-in-out;
  overflow: hidden;
  max-height: 500px;
}

.height-enter-from,
.height-leave-to {
  max-height: 42px;
  overflow: hidden;
  opacity: 0;
  margin-bottom: 0;
}
</style>
