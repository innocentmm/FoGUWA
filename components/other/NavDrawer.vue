<template>
  <v-navigation-drawer v-model="panel" temporary floating app>
    <v-list>
      <v-list-item
        v-for="(item, i) in items"
        :key="i"
        :to="item.to"
        router
        exact
        active-class="primary--text"
      >
        <v-list-item-action>
          <v-icon> {{ item.icon }} </v-icon>
        </v-list-item-action>
        <v-list-item-content>
          <v-list-item-title v-text="item.title" />
        </v-list-item-content>
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script>
export default {
  props: {
    value: Boolean
  },
  data: () => ({
    items: [
      {
        icon: 'map',
        title: 'Map',
        to: '/'
      },
      {
        icon: 'info',
        title: 'About',
        to: '/about'
      },
      {
        icon: 'info',
        title: 'Login',
        to: '/login'
      }
    ]
  }),
  computed: {
    panel: {
      get() {
        return this.value
      },
      set(value) {
        if (!value) {
          this.$emit('input', false)
        }
      }
    }
  }
}
</script>
