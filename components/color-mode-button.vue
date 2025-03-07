<script setup>
const wrapperEl = ref();
const colorMode = useColorMode();
const isDark = computed(() => colorMode.value === "dark");

const { width: windowWidth, height: windowHeight } = useWindowSize();
async function toggleMode() {
  await document.startViewTransition(() => {
    nextTick(() => {
      colorMode.preference = colorMode.value === "dark" ? "light" : "dark";
    });
  }).ready;

  const { top, left, width, height } = wrapperEl.value.getBoundingClientRect();
  const x = left + width / 2;
  const y = top + height / 2;
  const right = windowWidth.value - x;
  const bottom = windowHeight.value - y;

  const radius = Math.hypot(Math.max(x, right), Math.max(y, bottom));

  document.documentElement.animate(
    {
      clipPath: [`circle(0px at ${x}px ${y}px)`, `circle(${radius}px at ${x}px ${y}px)`],
    },
    {
      duration: 600,
      easing: "ease-in-out",
      pseudoElement: "::view-transition-new(root)",
    }
  );
}
</script>

<template>
  <ClientOnly>
    <div ref="wrapperEl">
      <UButton
        :icon="isDark ? 'i-hugeicons-moon-02' : 'i-hugeicons-sun-03'"
        color="neutral"
        variant="ghost"
        class="cursor-pointer"
        @click="toggleMode"
      />
    </div>
  </ClientOnly>
</template>

<style>
::view-transition-old(root),
::view-transition-new(root) {
  animation: none;
  mix-blend-mode: normal;
}
</style>
