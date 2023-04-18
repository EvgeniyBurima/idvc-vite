<template>
  <header>
    <div>
      <h1>Test Case Using a Modal</h1>
    </div>
  </header>

  <main>
    <IdscanContainer />
    <button @click = "openModal">Push second</button>
  </main>
</template>

<style scoped>

</style>

<script>
import IDVC from './components/idvc.vue'
import {container,openModal} from "jenesius-vue-modal";

export default {
  components: {IdscanContainer: container},
  name: "App",
  setup(){
    return {
      openModal: () => openModal(IDVC)
    }
  }
}
</script>
