<script setup>
import { ChevronRight, Dot } from 'lucide-vue-next'
import { computed } from 'vue'

const props = defineProps({
  data: { type: Object, required: true },
  isOpen: { type: Boolean, required: true },
  isCollapsed: { type: Boolean, required: true },
  isHovering: { type: Boolean, required: true },
})
const showFull = computed(() => props.isCollapsed || props.isHovering)
</script>

<template>
  <li class="side-menu__group side-group">
    <RouterLink
      :to="{ name: data.routeName }"
      class="side-title nav-btn"
      active-class="side-title--active"
      exact-active-class="side-title--exact-active"
      @click="$emit('toggle')"
    >
      <div class="side-title__label">
        <div class="side-icon icon-flex">
          <component :is="data.icon" :size="20" />
        </div>
        <div class="side-title__name text-smooth-animation" :class="{ hidden: !isHovering }">
          {{ data.title }}
        </div>
      </div>
      <div v-if="isHovering && data.list" class="nav-icon icon-flex">
        <ChevronRight :size="20" :class="{ rotated: isOpen }" />
      </div>
    </RouterLink>
    <ul
      v-if="data.list"
      class="side-group__list"
      :class="showFull ? (isOpen ? 'is-open' : '') : ''"
    >
      <li
        v-for="(item, index) in data.list"
        :key="index"
        class="side-group__list-item"
        :style="{ transitionDelay: `${index * 50}ms` }"
      >
        <RouterLink
          :to="{ name: item.path }"
          class="side-subtitle nav-btn"
          active-class="side-subtitle--active"
          exact-active-class="side-subtitle--exact-active"
        >
          <div class="side-subtitle__label">
            <div class="nav-icon icon-flex"><Dot :size="20" /></div>
            <div
              v-if="showFull"
              class="d-inline-block text-truncate side-subtitle__name"
              style="max-width: 9.375rem"
              :title="item.name"
            >
              {{ item.name }}
            </div>
          </div>
        </RouterLink>
      </li>
    </ul>
  </li>
</template>

<style lang="scss" scoped>
.side-group {
  @include flex-column-gap(2px);
}

// Заголовок группы
.side-title,
.side-subtitle {
  @include flex-row-gap(0, center, space-between);
  cursor: pointer;

  color: var(--color-primary-text);
  text-decoration: none;

  &__label {
    @include flex-row-gap($padding-internal, center, space-between);
  }
}
.side-title--active {
  color: var(--bs-primary);
  background-color: var(--bs-primary-bg-subtle);
}
.side-subtitle--active .nav-icon,
.side-subtitle--active .side-subtitle__name {
  color: var(--bs-primary);
}

// Кнопка навигации
.nav-btn {
  padding: $padding-internal $padding-external;
  border-radius: $radius-small;

  transition: all $transition;
  overflow: hidden;

  &:not(.side-title--active):hover {
    background-color: var(--color-secondary-background);
  }
  &.side-title--active:hover {
    background-color: var(--bs-primary-border-subtle);
  }
}

// Название
.side-title__name {
  white-space: nowrap;
}

// Анимация иконки
.nav-icon svg {
  transition: transform 0.3s ease;
}
.rotated {
  transform: rotate(90deg);
}

// Анимация для списка
.side-group__list {
  overflow: hidden;
  max-height: 0;
  opacity: 0;
  padding: 0;
  margin: 0;
  list-style: none;

  transition:
    max-height 0.5s ease,
    opacity 0.5s ease-in-out;
}

.side-group__list.is-open {
  max-height: 320px;
  opacity: 1;
  overflow-y: auto;
}

// Элементы списка с задержкой
.side-group__list-item {
  opacity: 0;
  transform: translateY(-10px);

  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
}

.side-group__list.is-open .side-group__list-item {
  opacity: 1;
  transform: translateY(0);
}
</style>
