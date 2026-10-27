<template>
  <div class="app-shell" :class="{ 'is-collapsed': isCollapsed }">
    <!-- Backdrop only exists on narrow screens while the drawer is open -->
    <div
      v-if="isSidebarOpen"
      class="sidebar-backdrop"
      aria-hidden="true"
      @click="closeSidebar"
    ></div>

    <aside id="app-sidebar" class="sidebar" :class="{ 'is-open': isSidebarOpen }">
      <div class="sidebar-brand">
        <!-- Brand text stays in the DOM when collapsed (visually hidden) so the heading is still announced -->
        <div class="brand-text">
          <h1 class="brand-name">{{ t('nav.companyName') }}</h1>
          <span class="brand-subtitle">{{ t('nav.subtitle') }}</span>
        </div>
        <button
          type="button"
          class="collapse-toggle"
          aria-controls="app-sidebar"
          :aria-expanded="isCollapsed ? 'false' : 'true'"
          aria-label="Toggle sidebar width"
          :title="isCollapsed ? 'Expand sidebar' : 'Collapse sidebar'"
          @click="toggleCollapsed"
        >
          <svg class="collapse-icon" viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M15 6l-6 6 6 6" />
          </svg>
        </button>
      </div>

      <nav class="sidebar-nav" aria-label="Primary">
        <router-link to="/" :class="{ active: $route.path === '/' }" :title="isCollapsed ? t('nav.overview') : null">
          <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <rect x="3" y="3" width="7" height="9" rx="1.5" />
            <rect x="14" y="3" width="7" height="5" rx="1.5" />
            <rect x="14" y="12" width="7" height="9" rx="1.5" />
            <rect x="3" y="16" width="7" height="5" rx="1.5" />
          </svg>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>
        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }" :title="isCollapsed ? t('nav.inventory') : null">
          <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M21 8l-9-5-9 5 9 5 9-5z" />
            <path d="M3 8v8l9 5 9-5V8" />
            <path d="M12 13v8" />
          </svg>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>
        <router-link to="/orders" :class="{ active: $route.path === '/orders' }" :title="isCollapsed ? t('nav.orders') : null">
          <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <rect x="5" y="3" width="14" height="18" rx="2" />
            <path d="M9 8h6M9 12h6M9 16h3" />
          </svg>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>
        <router-link to="/spending" :class="{ active: $route.path === '/spending' }" :title="isCollapsed ? t('nav.finance') : null">
          <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <rect x="2" y="5" width="20" height="14" rx="2" />
            <circle cx="12" cy="12" r="3" />
            <path d="M6 9v.01M18 15v.01" />
          </svg>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>
        <router-link to="/demand" :class="{ active: $route.path === '/demand' }" :title="isCollapsed ? t('nav.demandForecast') : null">
          <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M3 17l6-6 4 4 8-8" />
            <path d="M15 7h6v6" />
          </svg>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>
        <router-link to="/reports" :class="{ active: $route.path === '/reports' }" :title="isCollapsed ? 'Reports' : null">
          <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M14 3H7a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V8l-5-5z" />
            <path d="M14 3v5h5" />
            <path d="M9 13h6M9 17h4" />
          </svg>
          <span class="nav-label">Reports</span>
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <button
          type="button"
          class="theme-toggle"
          :aria-pressed="isDark ? 'true' : 'false'"
          :title="isDark ? 'Switch to light theme' : 'Switch to dark theme'"
          @click="toggleTheme"
        >
          <!-- Sun when dark (action: go light), moon when light (action: go dark) -->
          <svg v-if="isDark" class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <circle cx="12" cy="12" r="4" />
            <path d="M12 2v2M12 20v2M4.9 4.9l1.4 1.4M17.7 17.7l1.4 1.4M2 12h2M20 12h2M4.9 19.1l1.4-1.4M17.7 6.3l1.4-1.4" />
          </svg>
          <svg v-else class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M21 12.8A9 9 0 1 1 11.2 3a7 7 0 0 0 9.8 9.8z" />
          </svg>
          <span class="theme-label">Dark mode</span>
        </button>
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="content-column">
      <!-- Mobile-only top bar holding the drawer toggle -->
      <header class="topbar">
        <button
          type="button"
          class="menu-button"
          aria-controls="app-sidebar"
          :aria-expanded="isSidebarOpen ? 'true' : 'false'"
          aria-label="Toggle navigation"
          @click="toggleSidebar"
        >
          <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" aria-hidden="true">
            <path d="M4 6h16M4 12h16M4 18h16" />
          </svg>
        </button>
        <span class="topbar-title">{{ t('nav.companyName') }}</span>
      </header>
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount, computed, watch } from 'vue'
import { useRoute } from 'vue-router'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const route = useRoute()
    const isSidebarOpen = ref(false)

    // Desktop collapse state. Persisted in localStorage; every access is guarded because
    // storage can throw (private mode, blocked cookies, quota).
    const COLLAPSE_KEY = 'sidebarCollapsed'
    const readStoredCollapsed = () => {
      try {
        const v = window.localStorage.getItem(COLLAPSE_KEY)
        return v === 'true' ? true : v === 'false' ? false : null
      } catch (e) {
        return null
      }
    }
    // Nothing stored: default to icon-only on medium widths (901-1200px) to give content room
    const isCollapsed = ref(
      readStoredCollapsed() ?? window.matchMedia('(min-width: 901px) and (max-width: 1200px)').matches
    )
    // Theme: stored choice wins, otherwise follow the OS preference. Applied on <html> so
    // every component (scoped or global) sees the same tokens. Guarded storage as above.
    const THEME_KEY = 'theme'
    const readStoredTheme = () => {
      try {
        const v = window.localStorage.getItem(THEME_KEY)
        return v === 'dark' || v === 'light' ? v : null
      } catch (e) {
        return null
      }
    }
    const isDark = ref(
      (readStoredTheme() ?? (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light')) === 'dark'
    )
    const applyTheme = () => {
      document.documentElement.dataset.theme = isDark.value ? 'dark' : 'light'
    }
    applyTheme() // before first paint of the app to avoid a light flash
    const toggleTheme = () => {
      isDark.value = !isDark.value
      applyTheme()
      try {
        window.localStorage.setItem(THEME_KEY, isDark.value ? 'dark' : 'light')
      } catch (e) {
        // best-effort persistence
      }
    }

    const toggleCollapsed = () => {
      isCollapsed.value = !isCollapsed.value
      try {
        window.localStorage.setItem(COLLAPSE_KEY, String(isCollapsed.value))
      } catch (e) {
        // Persistence is best-effort; the in-memory state still works
      }
    }
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const apiTasks = ref([])

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    const closeSidebar = () => { isSidebarOpen.value = false }
    const toggleSidebar = () => { isSidebarOpen.value = !isSidebarOpen.value }

    // Close the mobile drawer after navigating
    watch(() => route.path, closeSidebar)

    // Escape closes the drawer (no-op on desktop where it is never "open")
    const onKeydown = (e) => {
      if (e.key === 'Escape' && isSidebarOpen.value) closeSidebar()
    }

    // If the viewport grows past the breakpoint while open, reset so state does not linger
    const mql = window.matchMedia('(min-width: 901px)')
    const onBreakpoint = (e) => { if (e.matches) closeSidebar() }

    onMounted(() => {
      loadTasks()
      window.addEventListener('keydown', onKeydown)
      mql.addEventListener('change', onBreakpoint)
    })

    onBeforeUnmount(() => {
      window.removeEventListener('keydown', onKeydown)
      mql.removeEventListener('change', onBreakpoint)
    })

    return {
      t,
      isSidebarOpen,
      isCollapsed,
      isDark,
      toggleTheme,
      toggleCollapsed,
      closeSidebar,
      toggleSidebar,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask
    }
  }
}
</script>

<style>
:root {
  /* Spacing: 4px base scale */
  --space-1: 4px; --space-2: 8px; --space-3: 12px; --space-4: 16px;
  --space-5: 24px; --space-6: 32px; --space-7: 48px;

  /* Layout */
  --sidebar-width: 240px;
  --sidebar-width-collapsed: 64px;
  --topbar-height: 56px;
  --content-max-width: 1600px;
  /* Offset for sticky children of the content column (FilterBar). Desktop has no topbar. */
  --sticky-top: 0px;

  /* Surfaces and text */
  --color-bg: #f8fafc;
  --color-surface: #ffffff;
  --color-surface-muted: #f8fafc;
  --color-border: #e2e8f0;
  --color-border-strong: #cbd5e1;
  --color-track: #f1f5f9;
  --color-text: #0f172a;
  --color-text-secondary: #475569;
  --color-text-muted: #64748b;
  --color-accent: #2563eb;
  --color-accent-soft: #eff6ff;
  --color-accent-strong: #1d4ed8;
  /* Neutral data-series colour (bars, card accents): dark ink in light, mid slate in dark */
  --color-series-neutral: #0f172a;
  color-scheme: light;
  --color-sidebar-bg: #0f172a;
  --color-sidebar-text: #cbd5e1;
  --color-sidebar-border: rgba(255, 255, 255, 0.1);
  --color-sidebar-hover-bg: rgba(255, 255, 255, 0.06);
  --color-sidebar-active-bg: rgba(255, 255, 255, 0.1);

  /* Shape and depth */
  --radius-sm: 6px; --radius-md: 8px; --radius-lg: 12px;
  --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.06);
  --shadow-md: 0 4px 12px rgba(15, 23, 42, 0.08);

  /* Type */
  --font-sans: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  --text-xs: 12px; --text-sm: 14px; --text-md: 16px; --text-lg: 20px; --text-xl: 24px;

  /* Stacking: filter bar < topbar < backdrop < sidebar < dropdowns (inside sidebar) < modals (1000-2000) */
  --z-filterbar: 90;
  --z-topbar: 95;
  --z-backdrop: 190;
  --z-sidebar: 200;
}

/* Dark theme: only the semantic tokens change; components reference tokens, not raw hex.
   Set via <html data-theme="dark"> (see useTheme logic in App.vue setup). */
:root[data-theme='dark'] {
  --color-bg: #0b1220;
  --color-surface: #111a2e;
  --color-surface-muted: #16213a;
  --color-border: #243049;
  --color-border-strong: #334155;
  --color-track: #1e293b;
  --color-text: #f1f5f9;
  --color-text-secondary: #cbd5e1;
  --color-text-muted: #94a3b8;
  --color-accent: #3b82f6;
  --color-accent-soft: rgba(59, 130, 246, 0.16);
  --color-accent-strong: #60a5fa;
  --color-series-neutral: #94a3b8;
  --color-sidebar-bg: #0a0f1c;
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.4);
  --shadow-md: 0 4px 12px rgba(0, 0, 0, 0.45);
  color-scheme: dark;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: var(--font-sans);
  background: var(--color-bg);
  color: var(--color-text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* Visible keyboard focus for every interactive element; mouse clicks stay clean */
:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}

.app-shell {
  display: grid;
  /* --sidebar-current drives the column so the content column follows the sidebar width */
  --sidebar-current: var(--sidebar-width);
  grid-template-columns: var(--sidebar-current) minmax(0, 1fr);
  min-height: 100vh;
  transition: grid-template-columns 0.2s ease;
}

.app-shell.is-collapsed {
  --sidebar-current: var(--sidebar-width-collapsed);
}

/* ---------- Sidebar ---------- */
.sidebar {
  position: sticky;
  top: 0;
  height: 100vh;
  height: 100dvh; /* avoids mobile address-bar overflow; falls back to 100vh above */
  display: flex;
  flex-direction: column;
  background: var(--color-sidebar-bg);
  color: var(--color-sidebar-text);
  z-index: var(--z-sidebar);
}

.sidebar-brand {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-2);
  padding: var(--space-5) var(--space-4) var(--space-4);
  border-bottom: 1px solid var(--color-sidebar-border);
}

.brand-name {
  font-size: var(--text-lg);
  font-weight: 700;
  color: #ffffff;
  letter-spacing: -0.025em;
  line-height: 1.2;
}

.brand-subtitle {
  display: block;
  margin-top: var(--space-1);
  font-size: var(--text-xs);
  color: #94a3b8;
}

/* Nav scrolls on short screens; the footer stays pinned and un-clipped so its dropdowns can open upward */
.sidebar-nav {
  flex: 1;
  min-height: 0;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
  padding: var(--space-4) var(--space-3);
}

.sidebar-nav a {
  position: relative;
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-2) var(--space-3);
  min-height: 40px;
  color: var(--color-sidebar-text);
  text-decoration: none;
  font-size: var(--text-sm);
  font-weight: 500;
  border-radius: var(--radius-md);
  transition: background-color 0.15s ease, color 0.15s ease;
}

.sidebar-nav a:hover {
  background: var(--color-sidebar-hover-bg);
  color: #ffffff;
}

.sidebar-nav a.active {
  background: var(--color-sidebar-active-bg);
  color: #ffffff;
}

/* 3px accent bar so the active state is not conveyed by color alone */
.sidebar-nav a.active::before {
  content: '';
  position: absolute;
  left: calc(var(--space-3) * -1);
  top: 8px;
  bottom: 8px;
  width: 3px;
  border-radius: 0 3px 3px 0;
  background: #60a5fa;
}

.sidebar-nav a:focus-visible,
.menu-button:focus-visible {
  outline-color: #93c5fd;
  outline-offset: -2px;
}

.collapse-toggle {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 32px;
  height: 32px;
  background: transparent;
  border: 1px solid var(--color-sidebar-border);
  border-radius: var(--radius-md);
  color: var(--color-sidebar-text);
  cursor: pointer;
  transition: background-color 0.15s ease, color 0.15s ease;
}

.collapse-toggle:hover {
  background: var(--color-sidebar-hover-bg);
  color: #ffffff;
}

.collapse-toggle:focus-visible {
  outline: 2px solid #93c5fd;
  outline-offset: 2px;
}

.collapse-icon {
  transition: transform 0.2s ease;
}

.nav-icon {
  width: 20px;
  height: 20px;
  flex-shrink: 0;
}

.theme-toggle {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  width: 100%;
  padding: var(--space-2) var(--space-3);
  min-height: 40px;
  background: transparent;
  border: 1px solid transparent;
  border-radius: var(--radius-md);
  color: var(--color-sidebar-text);
  font-family: inherit;
  font-size: var(--text-sm);
  font-weight: 500;
  cursor: pointer;
}

.theme-toggle:hover {
  background: var(--color-sidebar-hover-bg);
  color: #ffffff;
}

.theme-toggle:focus-visible {
  outline: 2px solid #93c5fd;
  outline-offset: 2px;
}

.sidebar-footer {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  padding: var(--space-3);
  border-top: 1px solid var(--color-sidebar-border);
}

/* ---------- Content column ---------- */
.content-column {
  display: flex;
  flex-direction: column;
  min-width: 0; /* lets wide tables scroll inside instead of stretching the grid */
}

.topbar,
.sidebar-backdrop {
  display: none;
}

.main-content {
  flex: 1;
  max-width: var(--content-max-width);
  width: 100%;
  margin: 0 auto;
  padding: var(--space-5) var(--space-6);
}

/* ---------- Collapsed (icons-only), desktop/tablet only ---------- */
/* Wrapped in a min-width query so the mobile drawer is always full width and labelled,
   even if a collapsed state was persisted from a wider viewport. */
@media (min-width: 901px) {
  .is-collapsed .collapse-icon {
    transform: rotate(180deg);
  }

  /* Visually hidden but still read by screen readers (unlike display:none) */
  .is-collapsed .brand-text,
  .is-collapsed .nav-label,
  .is-collapsed .sidebar-footer .profile-name,
  .is-collapsed .sidebar-footer .language-label,
  .is-collapsed .theme-label,
  .is-collapsed .sidebar-footer .chevron {
    position: absolute;
    width: 1px;
    height: 1px;
    margin: -1px;
    padding: 0;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
  }

  .is-collapsed .sidebar-brand {
    justify-content: center;
    padding-left: var(--space-3);
    padding-right: var(--space-3);
  }

  .is-collapsed .theme-toggle,
  .is-collapsed .sidebar-nav a {
    justify-content: center;
    padding-left: 0;
    padding-right: 0;
  }

  /* Footer triggers shrink to square icon buttons (scoped child styles are overridden by specificity) */
  .is-collapsed .sidebar-footer .profile-button,
  .is-collapsed .sidebar-footer .language-button {
    justify-content: center;
    padding: 3px;
  }

  .is-collapsed .sidebar-footer .language-button {
    padding: 8px 0;
  }

  /* The 64px sidebar is too narrow for the menus, so let them extend over the content.
     The sidebar sits above content (z-index) and has no overflow clipping on the footer. */
  .is-collapsed .sidebar-footer .dropdown-menu {
    right: auto;
    left: 0;
    width: 240px;
  }
}

/* ---------- Responsive: off-canvas drawer ---------- */
@media (max-width: 900px) {
  :root {
    --sticky-top: var(--topbar-height);
  }

  .app-shell {
    grid-template-columns: minmax(0, 1fr);
  }

  .sidebar {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    width: min(var(--sidebar-width), 85vw);
    transform: translateX(-100%);
    /* visibility removes the hidden drawer from the tab order once the slide-out ends */
    visibility: hidden;
    transition: transform 0.2s ease, visibility 0s linear 0.2s;
  }

  .sidebar.is-open {
    transform: translateX(0);
    visibility: visible;
    transition: transform 0.2s ease, visibility 0s;
  }

  .sidebar-backdrop {
    display: block;
    position: fixed;
    inset: 0;
    background: rgba(15, 23, 42, 0.5);
    z-index: var(--z-backdrop);
  }

  /* The drawer is toggled by the topbar menu button instead */
  .collapse-toggle {
    display: none;
  }

  .topbar {
    display: flex;
    align-items: center;
    gap: var(--space-3);
    position: sticky;
    top: 0;
    height: var(--topbar-height);
    padding: 0 var(--space-4);
    background: var(--color-surface);
    border-bottom: 1px solid var(--color-border);
    z-index: var(--z-topbar);
  }

  .menu-button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 40px;
    height: 40px;
    background: transparent;
    border: 1px solid var(--color-border);
    border-radius: var(--radius-md);
    color: var(--color-text);
    cursor: pointer;
  }

  .topbar-title {
    font-weight: 700;
    color: var(--color-text);
  }

  .main-content {
    padding: var(--space-4);
  }
}

@media (prefers-reduced-motion: reduce) {
  .sidebar,
  .app-shell,
  .collapse-icon {
    transition: none;
  }
}

.page-header {
  margin-bottom: 1.5rem;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: var(--color-text);
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: var(--color-text-muted);
  font-size: 0.938rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: var(--color-surface);
  padding: 1.25rem;
  border-radius: var(--radius-lg);
  border: 1px solid var(--color-border);
  box-shadow: var(--shadow-sm);
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: var(--color-border-strong);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
}

.stat-label {
  color: var(--color-text-muted);
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value {
  color: #ea580c;
}

.stat-card.success .stat-value {
  color: #059669;
}

.stat-card.danger .stat-value {
  color: #dc2626;
}

.stat-card.info .stat-value {
  color: #2563eb;
}

.card {
  background: var(--color-surface);
  border-radius: var(--radius-lg);
  padding: 1.25rem;
  border: 1px solid var(--color-border);
  box-shadow: var(--shadow-sm);
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  flex-wrap: wrap; /* title + actions/legend stack on narrow screens instead of overflowing */
  gap: var(--space-2) var(--space-4);
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid var(--color-border);
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.025em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--color-surface-muted);
  border-top: 1px solid var(--color-border);
  border-bottom: 1px solid var(--color-border);
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: var(--color-text-secondary);
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid var(--color-track);
  color: var(--color-text-secondary);
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: var(--color-surface-muted);
}

.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: #d1fae5;
  color: #065f46;
}

.badge.warning {
  background: #fed7aa;
  color: #92400e;
}

.badge.danger {
  background: #fecaca;
  color: #991b1b;
}

.badge.info {
  background: #dbeafe;
  color: #1e40af;
}

.badge.increasing {
  background: #d1fae5;
  color: #065f46;
}

.badge.decreasing {
  background: #fecaca;
  color: #991b1b;
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: #fecaca;
  color: #991b1b;
}

.badge.medium {
  background: #fed7aa;
  color: #92400e;
}

.badge.low {
  background: #dbeafe;
  color: #1e40af;
}

.loading {
  text-align: center;
  padding: 3rem;
  color: #64748b;
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: 1rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.938rem;
}
</style>
