<script setup>
import { ref, computed, onMounted, onBeforeUnmount, nextTick } from 'vue'
import cvRaw from './data/cv.md?raw'
import fm from 'front-matter'
import { marked } from 'marked'

/* ---------- Parse seguro do Markdown/Front-matter ---------- */
const { attributes = {}, body = '' } = (() => {
  try { return fm(cvRaw) } catch { return { attributes: {}, body: '' } }
})()

/* ---------- Derivados (seguros) ---------- */
const name = computed(() => typeof attributes.name === 'string' ? attributes.name : '')
const title = computed(() => typeof attributes.title === 'string' ? attributes.title : '')
const links = computed(() => attributes.links && typeof attributes.links === 'object' ? attributes.links : {})
const profile = computed(() => {
  const p = attributes.profile
  return typeof p === 'string' ? p : (p == null ? '' : String(p))
})
const skills = computed(() => Array.isArray(attributes.skills) ? attributes.skills : [])
const experience = computed(() => Array.isArray(attributes.experience) ? attributes.experience : [])
const education = computed(() => Array.isArray(attributes.education) ? attributes.education : [])
const training = computed(() => (attributes.training && typeof attributes.training === 'object' && !Array.isArray(attributes.training)) ? attributes.training : {})

/* ---------- Markdown → HTML ---------- */
const mdHtml = ref('')
marked.setOptions({ gfm: true, breaks: true, headerIds: true, mangle: false })

/* ---------- Tema (dark/light) ---------- */
const theme = ref('light')
function applyTheme(t) {
  const isDark = t === 'dark'
  document.documentElement.classList.toggle('dark', isDark)
  document.documentElement.style.colorScheme = isDark ? 'dark' : 'light'
}
function initTheme() {
  const saved = localStorage.getItem('theme')
  const prefersDark = window.matchMedia?.('(prefers-color-scheme: dark)').matches
  theme.value = saved || (prefersDark ? 'dark' : 'light')
  applyTheme(theme.value)
}
function toggleTheme() {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
  localStorage.setItem('theme', theme.value)
  applyTheme(theme.value)
}

/* ---------- Print sempre em light ---------- */
let prevTheme = null
function forceLightForPrint() {
  prevTheme = document.documentElement.classList.contains('dark') ? 'dark' : 'light'
  document.documentElement.classList.remove('dark')
  document.documentElement.style.colorScheme = 'light'
}
function restoreThemeAfterPrint() {
  if (prevTheme === 'dark') {
    document.documentElement.classList.add('dark')
    document.documentElement.style.colorScheme = 'dark'
  } else {
    document.documentElement.classList.remove('dark')
    document.documentElement.style.colorScheme = 'light'
  }
}

/* ---------- UI helpers ---------- */
const initials = computed(() =>
  (name.value || 'RD').trim().split(/\s+/).map(s => s[0]).slice(0, 2).join('').toUpperCase()
)
const roleBadge = computed(() => {
  const t = title.value || 'Especialista IT'
  return t.length > 28 ? 'Especialista IT' : t
})

/* ---------- Lifecycle ---------- */
onMounted(async () => {
  initTheme()
  mdHtml.value = marked.parse(body || '')
  document.title = name.value ? `${name.value} — CV` : 'CV'

  // links do markdown → nova aba
  await nextTick()
  document.querySelectorAll('main a[href^="http"]').forEach(a => {
    a.setAttribute('target', '_blank')
    a.setAttribute('rel', 'noopener noreferrer')
    a.classList.add('link')
  })

  // eventos de impressão → light
  window.addEventListener('beforeprint', forceLightForPrint)
  window.addEventListener('afterprint', restoreThemeAfterPrint)
  const mql = window.matchMedia?.('print')
  if (mql && 'addEventListener' in mql) {
    mql.addEventListener('change', e => e.matches ? forceLightForPrint() : restoreThemeAfterPrint())
  }
})

onBeforeUnmount(() => {
  window.removeEventListener('beforeprint', forceLightForPrint)
  window.removeEventListener('afterprint', restoreThemeAfterPrint)
})
</script>

<template>
  <div class="min-h-screen bg-slate-50 text-slate-800 dark:bg-slate-950 dark:text-slate-100">

    <!-- Topbar (não imprime) -->
    <div class="sticky top-0 z-10 bg-white/80 dark:bg-slate-900/80 backdrop-blur
                border-b border-slate-200 dark:border-slate-800 px-4 print:hidden">
      <div class="mx-auto max-w-3xl py-3 flex items-center justify-between gap-3">
        <!-- Avatar + nome + badge -->
        <div class="flex items-center gap-3 min-w-0">
          <div class="w-9 h-9 flex items-center justify-center rounded-full
                      bg-gradient-to-br from-sky-600 to-indigo-600 text-white
                      font-bold shadow-sm ring-2 ring-white/70 dark:ring-slate-800 select-none">
            {{ initials }}
          </div>
          <div class="leading-tight min-w-0">
            <div class="text-sm font-semibold text-slate-800 dark:text-slate-200 truncate">
              {{ name || '—' }}
            </div>
            <div class="flex items-center gap-2">
              <span class="text-xs text-slate-500 dark:text-slate-400">CV Online</span>
              <span class="px-2 py-0.5 text-[10px] rounded-full
                           bg-sky-100 text-sky-700
                           dark:bg-sky-900/60 dark:text-sky-300 whitespace-nowrap">
                {{ roleBadge }}
              </span>
            </div>
          </div>
        </div>

        <!-- Links rápidos + tema -->
        <div class="flex items-center gap-3">
          <button class="px-3 py-1.5 rounded-xl border border-slate-300 hover:bg-slate-100 text-sm
                         dark:border-slate-600 dark:hover:bg-slate-800" @click="toggleTheme"
            aria-label="Alternar tema" title="Alternar tema">
            {{ theme === 'dark' ? '🌙 Dark' : '☀️ Light' }}
          </button>
        </div>
      </div>
    </div>

    <!-- Página -->
    <main class="mx-auto max-w-3xl p-6 md:p-10 my-6 bg-white shadow-sm rounded-2xl
                  dark:bg-slate-900 dark:shadow-none print:shadow-none print:rounded-none">

      <!-- Cabeçalho -->
      <header class="grid grid-cols-1 md:grid-cols-[1fr_auto] items-start gap-4
                     print:grid-cols-[1fr_auto] print:gap-4">
        <div>
          <h1 class="text-3xl font-bold tracking-tight text-sky-700 dark:text-sky-400">{{ name }}
          </h1>
          <p v-if="title" class="text-slate-600 dark:text-slate-300">{{ title }}</p>
        </div>
        <ul class="text-sm text-slate-600 dark:text-slate-300 md:text-right leading-6">
          <li v-if="links.email" class="whitespace-nowrap">
            <span class="font-medium">Email:&nbsp;</span>
            <a class="link" :href="`mailto:${links.email}`" target="_blank"
              rel="noopener noreferrer">{{ links.email }}</a>
          </li>
          <li v-if="links.linkedin" class="whitespace-nowrap">
            <span class="font-medium">LinkedIn:&nbsp;</span>
            <a class="link break-all md:break-normal" :href="links.linkedin" target="_blank"
              rel="noopener noreferrer">{{ links.linkedin }}</a>
          </li>
          <li v-if="links.github" class="whitespace-nowrap">
            <span class="font-medium">GitHub:&nbsp;</span>
            <a class="link break-all md:break-normal" :href="links.github" target="_blank"
              rel="noopener noreferrer">{{ links.github }}</a>
          </li>
          <li v-if="attributes.location" class="whitespace-nowrap">
            <span class="font-medium">Localização:&nbsp;</span>{{ attributes.location }}
          </li>
        </ul>
      </header>

      <hr class="my-6 border-slate-200 dark:border-slate-800" />

      <!-- Perfil -->
      <section v-if="profile">
        <h2 class="text-lg font-semibold">Perfil</h2>
        <p class="mt-2 text-[15px] leading-relaxed text-slate-700 dark:text-slate-300">{{ profile }}
        </p>
      </section>

      <!-- Skills -->
      <section v-if="skills.length" class="mt-6">
        <h2 class="text-lg font-semibold">Competências Técnicas</h2>
        <div class="mt-3 flex flex-wrap gap-2">
          <span v-for="(s, i) in skills" :key="i" class="pill">{{ s }}</span>
        </div>
      </section>

      <!-- Experiência -->
      <section v-if="experience.length" class="mt-6">
        <h2 class="text-lg font-semibold">Experiência Profissional</h2>
        <div class="mt-3 space-y-5">
          <article v-for="(exp, i) in experience" :key="i"
            class="p-4 rounded-xl shadow-sm bg-white dark:bg-slate-800 avoid-break">
            <header class="flex items-baseline justify-between gap-3 flex-wrap sm:flex-nowrap">
              <div class="min-w-0">
                <h3 class="font-semibold truncate">{{ exp.org }}</h3>
                <p class="text-sm text-slate-600 dark:text-slate-300 truncate">
                  {{ exp.role }} · {{ exp.period }}
                </p>
              </div>
              <span v-if="exp.sector" class="pill !text-[11px]">{{ exp.sector }}</span>
            </header>

            <ul v-if="Array.isArray(exp.bullets) && exp.bullets.length"
              class="mt-2 list-disc pl-5 text-[15px] leading-relaxed text-slate-700 dark:text-slate-300">
              <li v-for="(b, j) in exp.bullets" :key="j">{{ b }}</li>
            </ul>

            <div v-if="Array.isArray(exp.stack) && exp.stack.length"
              class="mt-2 flex flex-wrap gap-2">
              <span v-for="(tech, j) in exp.stack" :key="j" class="pill">{{ tech }}</span>
            </div>
          </article>
        </div>
      </section>

      <!-- Formação Académica -->
      <section v-if="education.length" class="mt-6">
        <h2 class="text-lg font-semibold">Formação Académica</h2>
        <div class="mt-3 grid gap-3">
          <div v-for="(e, i) in education" :key="i" class="text-[15px] leading-relaxed">
            <p class="font-medium">{{ e.title }}</p>
            <p class="text-slate-600 dark:text-slate-300">{{ e.org }} · {{ e.year }}</p>
            <p v-if="e.note" class="text-slate-600 dark:text-slate-300">{{ e.note }}</p>
          </div>
        </div>
      </section>

      <!-- Formação Profissional -->
      <section v-if="Object.keys(training).length" class="mt-6">
        <h2 class="text-lg font-semibold">Formação Profissional (seleção)</h2>
        <div class="mt-3 grid md:grid-cols-2 gap-4 text-[15px] leading-relaxed">
          <div v-if="Array.isArray(training.dev)">
            <h3 class="font-medium">Desenvolvimento / Web</h3>
            <ul class="list-disc pl-5 mt-1">
              <li v-for="(t, i) in training.dev" :key="i">{{ t }}</li>
            </ul>
          </div>
          <div v-if="Array.isArray(training.db)">
            <h3 class="font-medium">Bases de Dados</h3>
            <ul class="list-disc pl-5 mt-1">
              <li v-for="(t, i) in training.db" :key="i">{{ t }}</li>
            </ul>
          </div>
          <div v-if="Array.isArray(training.agile)">
            <h3 class="font-medium">Ágil / DevOps</h3>
            <ul class="list-disc pl-5 mt-1">
              <li v-for="(t, i) in training.agile" :key="i">{{ t }}</li>
            </ul>
          </div>
        </div>
      </section>

      <!-- Projetos (Markdown tipografado) -->
      <section
        class="mt-6 prose prose-slate prose-sm md:prose-base max-w-none dark:prose-invert avoid-break"
        v-html="mdHtml" />

      <footer class="mt-8 text-xs text-slate-500 dark:text-slate-400">
        <p>Última atualização: {{ new Date().toLocaleDateString('pt-PT') }}</p>
      </footer>
    </main>
  </div>
</template>
