<template>
  <v-app>
    <v-container fluid align-left>
      <h1>VTreeview examples</h1>
      <h2>Example 1: Simple tree</h2>
      <h3>Basic variant</h3>
      <v-treeview
        :items="items"
        caption-field="name"
        children-field="children"
      />
      <h3>Basic variant - expanded</h3>
      <v-treeview
        :items="items"
        caption-field="name"
        children-field="children"
        expand-all
      />
      <h3>Show root, icons and expand upon display</h3>
      <v-treeview
        :items="items"
        caption-field="name"
        children-field="children"
        icon-field="icon"
        root
        expand-all
      />
      <h2>Example 2: one item can be selected</h2>
      <v-treeview
        :items="items"
        caption-field="name"
        children-field="children"
        v-model="selectedItemsSingle"
        select
        keyField="id"
      />
      <div>
        Selected item ID: {{ selectedItemsSingle }}
      </div>
      <h2>Example 2B: one item can be selected (one child case)</h2>
      <v-treeview
        :items="itemsOneChild"
        caption-field="name"
        children-field="children"
        v-model="selectedItemsSingle"
        select
        keyField="id"
      />
      <div>
        Selected item ID: {{ selectedItemsSingle }}
      </div>
      <h2>Example 3: Multiple selection with checkboxes</h2>
      <h3>Basic variant</h3>
      <v-treeview
        :items="items"
        v-model="selectedItems"
        checkbox
      />
      <div>
        Selected items: {{ selectedItems }}
      </div>
      <h3>Showing root, icons, expand all</h3>
      <v-treeview
        :items="items"
        v-model="selectedItems"
        checkbox
        root
        expand-all
        icon-field="icon"
      />
      <div>
        Selected items: {{ selectedItems }}
      </div>
      <h2>Example 4: Dynamically loaded children</h2>
      <v-treeview
        :items="dynamicItems"
        loading-text="Please wait a bit!"
        :dynamicChildrenContext="dynamicChildrenContext"
      />
    </v-container>
  </v-app>
</template>

<script>
export default {
  name: 'app',
  data: () => ({
    items: {
      id: 0,
      name: 'Root',
      children: [
        {
          id: 1,
          name: 'First Child',
          children: null
        },
        {
          id: 2,
          name: 'Second Child',
          children: [
            {
              id: 3,
              name: 'Grandchild 1',
              icon: 'account_circle'
            },
            {
              id: 4,
              name: 'Grandchild 2',
              children: [
                {
                  id: 5,
                  name: 'GrandGrandchild 1'
                },
                {
                  id: 6,
                  name: 'GrandGrandchild 2',
                  children: [
                    {
                      id: 7,
                      name: 'GrandGrandGrandchild 1',
                      icon: 'home'
                    },
                    {
                      id: 8,
                      name: 'GrandGrandGrandchild 2'
                    }
                  ]
                }
              ]
            },
            {
              id: 9,
              name: 'Grandchild 3'
            }
          ]
        },
        {
          id: 10,
          name: 'Third Child'
        }
      ]
    },
    itemsOneChild: {
      id: 0,
      name: 'Root',
      children: [
        {
          id: 1,
          name: 'First Child',
          children: null
        },
        {
          id: 2,
          name: 'Second Child',
          children: [
            {
              id: 3,
              name: 'Grandchild 1',
              icon: 'account_circle'
            }
          ]
        }
      ]
    },
    selectedItems: [],
    selectedItemsSingle: [],
    dynamicallyLoadedChild: null
  }),
  computed: {
    dynamicChildrenContext () {
      return {
        // here we could have some object,
        // $store reference for dispatching an action
        // or something else...
      }
    },
    // BUG in eslint: https://github.com/vuejs/eslint-plugin-vue/issues/420
    // --> using eslint-disable-next-line
    dynamicItems () {
      // create a new tree, but reuse existing children
      let calculatedTreeItems = {
        id: 0,
        name: 'Root',
        children: [...this.items.children]
      }
      // let's make a new first child under the root which will have dynamic children
      let newFirstChild = {
        id: 100,
        name: 'Dynamic Children are here',
        children: null
      }
      // eslint-disable-next-line
      calculatedTreeItems.children.unshift(newFirstChild)
      // we now check if the child has already been loaded
      if (this.dynamicallyLoadedChild) {
        // child is loaded - then make the children a regular array
        newFirstChild.children = [this.dynamicallyLoadedChild]
      } else {
        // there is no child yet loaded -> point children to a function!
        newFirstChild.children = this.loadDynamicChild
      }
      return calculatedTreeItems
    }
  },
  methods: {
    sleep (ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    },
    async loadDynamicChild (context) {
      // sleep for 2 seconds
      // you could await some API here also!
      await this.sleep(2000)
      this.dynamicallyLoadedChild = {
        id: 101,
        name: 'I am dynamically loaded!',
        children: []
      }
      // return true if everything was ok
      return Promise.resolve(true)
    }
  }
}
</script>

<style lang="stylus">
#app
  font-family 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  color #2c3e50
</style>
