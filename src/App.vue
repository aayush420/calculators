<script setup lang="ts">
import { ref } from 'vue';
import { RouterLink, RouterView } from 'vue-router'

const currentTheme = ref('dark')
if (localStorage.theme === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
  setDarkTheme()
} else {
  setLightTheme()
}

function setLightTheme() {
  currentTheme.value = 'light'

  localStorage.setItem('theme', 'light')
  document.documentElement.classList.remove('dark')
}
function setDarkTheme() {
  currentTheme.value = 'dark'

  localStorage.setItem('theme', 'dark')
  document.documentElement.classList.add('dark')
}
function toggleTheme() {
  if (currentTheme.value == 'dark') {
    setLightTheme()
  } else {
    setDarkTheme()
  }
}
</script>

<template>
  <header
    class="sticky top-0 z-40 w-full backdrop-blur flex-none transition-colors duration-500 lg:z-50 lg:border-b lg:border-slate-900/10 dark:border-slate-50/[0.06] bg-white supports-backdrop-blur:bg-white/95 dark:bg-slate-900/75">
    <div class="max-w-8xl mx-auto">
      <div class="py-1 border-b border-slate-900/10 lg:px-8 lg:border-0 dark:border-slate-300/10 mx-4 lg:mx-0">
        <div class="relative flex items-center overflow-hidden">

          <img alt="Gannesoft" class="logo h-12" src="@/assets/logo.svg" />
          <span
            class="text-xs leading-5 font-semibold bg-slate-400/10 rounded-full py-1 px-3 flex items-center space-x-2 hover:bg-slate-400/20 dark:highlight-white/5">Private
            Limited</span>
          <button @click="toggleTheme" aria-label="Toggle themes" class="ml-auto">
            <span v-if="currentTheme == 'dark'">
              <!-- Sun icon -->
              <svg width="24" height="24" fill="none" aria-hidden="true">
                <path d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" fill="currentColor" stroke="currentColor" strokeWidth="2"
                  strokeLinecap="round" strokeLinejoin="round" />
                <path d="M12 4v1M18 6l-1 1M20 12h-1M18 18l-1-1M12 19v1M7 17l-1 1M5 12H4M7 7 6 6" stroke="currentColor"
                  strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" />
              </svg></span>
            <span v-else>
              <!-- Moon icon -->
              <svg width="24" height="24" fill="none" aria-hidden="true">
                <path
                  d="M18 15.63c-.977.52-1.945.481-3.13.481A6.981 6.981 0 0 1 7.89 9.13c0-1.185-.04-2.153.481-3.13C6.166 7.174 5 9.347 5 12.018A6.981 6.981 0 0 0 11.982 19c2.67 0 4.844-1.166 6.018-3.37ZM16 5c0 2.08-.96 4-3 4 2.04 0 3 .92 3 3 0-2.08.96-3 3-3-2.04 0-3-1.92-3-4Z"
                  fill="currentColor" stroke="currentColor" strokeWidth="2" strokeLinecap="round"
                  strokeLinejoin="round" />
              </svg></span>
          </button>
        </div>
      </div>
    </div>
  </header>
  <div class="flex pt-20 h-screen overflow-hidden">

    <nav
      class="hidden lg:block fixed z-20 inset-0 top-[3.8125rem] left-[max(0px,calc(50%-45rem))] right-auto w-[19rem] pb-10 pl-8 pr-6 overflow-y-auto">
      <RouterLink class="navLink" to="/">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
          stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round"
            d="m2.25 12 8.954-8.955c.44-.439 1.152-.439 1.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75M8.25 21h8.25" />
        </svg>
        <span>Home</span>
      </RouterLink>
      <RouterLink class="navLink" to="/about">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
          stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round"
            d="m11.25 11.25.041-.02a.75.75 0 0 1 1.063.852l-.708 2.836a.75.75 0 0 0 1.063.853l.041-.021M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Zm-9-3.75h.008v.008H12V8.25Z" />
        </svg>
        <span>About</span>
      </RouterLink>
      <div class="subnav">
        <h5 class="mb-8 lg:mb-3 font-semibold text-slate-900 dark:text-slate-200">Calculators</h5>
        <ul>

          <RouterLink class="navLink" to="/loan">

            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round"
                d="M15.75 15.75V18m-7.5-6.75h.008v.008H8.25v-.008Zm0 2.25h.008v.008H8.25V13.5Zm0 2.25h.008v.008H8.25v-.008Zm0 2.25h.008v.008H8.25V18Zm2.498-6.75h.007v.008h-.007v-.008Zm0 2.25h.007v.008h-.007V13.5Zm0 2.25h.007v.008h-.007v-.008Zm0 2.25h.007v.008h-.007V18Zm2.504-6.75h.008v.008h-.008v-.008Zm0 2.25h.008v.008h-.008V13.5Zm0 2.25h.008v.008h-.008v-.008Zm0 2.25h.008v.008h-.008V18Zm2.498-6.75h.008v.008h-.008v-.008Zm0 2.25h.008v.008h-.008V13.5ZM8.25 6h7.5v2.25h-7.5V6ZM12 2.25c-1.892 0-3.758.11-5.593.322C5.307 2.7 4.5 3.65 4.5 4.757V19.5a2.25 2.25 0 0 0 2.25 2.25h10.5a2.25 2.25 0 0 0 2.25-2.25V4.757c0-1.108-.806-2.057-1.907-2.185A48.507 48.507 0 0 0 12 2.25Z" />
            </svg>
            <span>Loan EMI Calculator</span>
          </RouterLink>
        </ul>
      </div>
    </nav>
    <RouterView class="h-auto overflow-auto lg:pl-[19.5rem]" />
  </div>

</template>

<style>
nav {
  @apply p-2;
}

.subnav {
  @apply mt-12 lg:mt-8;
}

.subnav ul {

  @apply space-y-6 lg:space-y-2 border-l border-slate-100 dark:border-slate-800
}

.navLink {
  /* color: #374151; */
  /* bg-green-300 */
  /* @apply flex gap-2 p-2 mt-2 rounded-lg w-10 md:w-64; */
  @apply flex h-8 border-l pl-4 -ml-px border-transparent hover:border-slate-400 dark:hover:border-slate-500 text-slate-700 hover:text-slate-900 dark:text-slate-400 dark:hover:text-slate-300;
}

/* .navLink:hover { */
/* color: #4f46e5; */
/* @apply bg-yellow-200 */
/* } */

.navLink.router-link-active {
  color: #4f46e5;
  /* @apply bg-green-400 */
  @apply flex border-l pl-4 -ml-px text-sky-500 border-current font-semibold dark:text-sky-400;
}

.navLink svg {
  @apply w-7 h-7 mr-2 stroke-slate-900 dark:stroke-slate-100 p-1 rounded-md ring-1 ring-slate-900/5 shadow-sm dark:ring-0 dark:shadow-none;
}

.navLink:hover svg,
.navLink.router-link-active svg {
  @apply shadow stroke-slate-600 dark:stroke-slate-300 ring-slate-900/10 dark:shadow-none shadow-sky-200 bg-sky-300 dark:bg-sky-500;
}

.navLink span {
  @apply hidden md:block;
}
</style>