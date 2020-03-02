
<template>

  <q-layout view="lHh Lpr lFf">
     <q-header class="gt-xs" elevated>
      <q-toolbar >
        <q-btn
          :ripple="{ color: 'yellow' }"
          flat
          push
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />
        
      </q-toolbar>
      <div class="q-px-md q-pt-lg q-mb-md">
        <div class="text-h3">Puissance 4</div>
        <div class="text-subtitle1">{{ todaysDate }}</div>
      </div>
      <q-img src="statics/road.jpg" class="header-image absolute-top"></q-img>
    </q-header>

    <q-header class="xs" elevated>
     <q-toolbar>
        <q-btn
          :ripple="{ color: 'yellow' }"
          flat
          push
          dense
          round
          icon="fas fa-dice-four"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />
      
        <p class="no-margin q-pl-sm" >Puissance 4</p>
      </q-toolbar>
    </q-header>
   
    <q-drawer
      gt-sm
      v-model="leftDrawerOpen"
      show-if-above
      :width="150"
      :breakpoint="600"
    >
      <q-scroll-area
        style="height: calc(100% - 168px); margin-top: 168px; border-right: 1px solid #ddd"
      >
        <q-list padding>
          <q-item to="/" exact clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="list" />
            </q-item-section>

            <q-item-section>
              Play
            </q-item-section>
          </q-item>

          <q-toggle
        v-model="toggleAi"
        checked-icon="check"
        color="positive"
        label="Toggle AI"
        unchecked-icon="clear"
      />
      
        </q-list>
      </q-scroll-area>

      <q-img class="absolute-top" src="statics/road.jpg" style="height: 168px">
        <div class="absolute-bottom bg-transparent">
          <q-avatar size="56px" class="q-mb-sm">
            <img
              src="https://www.gravatar.com/avatar/2730e0263df7f9a64340cb75fa71c7e5"
            />
          </q-avatar>
          <div class="text-weight-bold">Matthieu Degré</div>
          <div>matthieu.degre@gmail.com</div>
        </div>
      </q-img>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink'
import { date } from "quasar";

export default {
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  data () {
    return {
      isAiToggled: false,
      leftDrawerOpen: false,
      essentialLinks: [
        {
          title: 'Docs',
          caption: 'quasar.dev',
          icon: 'school',
          link: 'https://quasar.dev'
        },
        {
          title: 'Github',
          caption: 'github.com/quasarframework',
          icon: 'code',
          link: 'https://github.com/quasarframework'
        },
        {
          title: 'Discord Chat Channel',
          caption: 'chat.quasar.dev',
          icon: 'chat',
          link: 'https://chat.quasar.dev'
        },
        {
          title: 'Forum',
          caption: 'forum.quasar.dev',
          icon: 'record_voice_over',
          link: 'https://forum.quasar.dev'
        },
        {
          title: 'Twitter',
          caption: '@quasarframework',
          icon: 'rss_feed',
          link: 'https://twitter.quasar.dev'
        },
        {
          title: 'Facebook',
          caption: '@QuasarFramework',
          icon: 'public',
          link: 'https://facebook.quasar.dev'
        }
      ]
    }
  },
  watch: {
    toggleAi(newValue, oldValue) {
      console.log(`The state isAiActivated has changed form ${oldValue} to ${newValue}`)
    }
  },
    computed: {
    
    todaysDate() {
      let timeStamp = Date.now();
      return date.formatDate(timeStamp, "dddd D MMMM");
    },
     toggleAi: {
    get () {
      return this.$store.state.isAiToggled
    },
    set () {
      this.$store.commit('toggleAi')
    }
  },
   
  }
}
</script>
<style lang="scss">
.header-image {
  height: 100%;
  z-index: -1;
  opacity: 1;
}
</style>
