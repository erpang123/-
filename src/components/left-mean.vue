<script>
import demoModel from './demo-model'
import listItem from '@/components/list-item.vue'
export default {
  components: {
    listItem,
    demoModel
  },
  created () {
    this.createRoutes()
  },
  methods: {
    createRoutes () {
      const reduceFun = function (arr) {
        const routes = []
        for (let i = 0; i < arr.length; i++) {
          const el = arr[i]
          let routeObj = {}
          routeObj = {
            path: el.path,
            component: el.component
          }
          if (el.sonList) {
            const childRoute = reduceFun(el.sonList)
            routeObj.children = childRoute
          }
          routes.push(routeObj)
        }
        return routes
      }
      const routes = reduceFun(this.$store.getters.meanList)
      let oldR = this.$router.options.routes[1]
      oldR.children = routes
      this.$router.addRoutes([oldR])
    }
  },
  render (createElement) {
    let domArr = []
    let path = '/routeD'
    const reduceFun = function (arr, zIndex) {
      const lArr = []
      for (let i = 0; i < arr.length; i++) {
        const el = JSON.parse(JSON.stringify(arr[i]))
        let index
        let domList = null
        if (zIndex !== 1) {
          index = zIndex + '-' + i
        } else {
          index = i.toString()
        }
        if (!el.path.includes('/')) {
          el.path = path + '/' + el.path
        }
        if (el.sonList) {
          path = el.path
          const domObj = reduceFun(el.sonList, index)
          let arrPath = el.path.split('/')
          path = arrPath.slice(0,-1).join('/')
          domList = domObj.lArr
        }
        const dom = createElement('list-item', {
          props: {
            item: el,
            i: el.key || i.toString()
          }
        }, domList)
        lArr.push(dom)
      }
      return {
        lArr
      }
    }
    const obj = reduceFun(this.$store.getters.meanList, 1)
    domArr = obj.lArr
    return createElement('el-menu', {
      class: 'el-menu-vertical-demo',
      props: {
        'default-active': '1'
      },
      on: {
        select (obj) {
          console.log(obj)
        }
      }
    }, domArr)
  }
}
</script>
