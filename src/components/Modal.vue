<template>
  <div class="modal">
    <div class="modal__container">
      <div class="modal__inner" v-if="modalType === 1" :class="modalType === 1 ? 'is-success' : ''">
        <i class="icon-check-circle-outline"></i>
        Form gönderimi başarılı.
      </div>
      <div class="modal__inner" v-if="modalType === 0">
        <i class="icon-alert-circle"></i>
        Form gönderimi başarısız. Daha sonra tekrar deneyin.
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    modalType: Number,
    modalDetail: Object
  },
  mounted () {
    this.modalClose('.modal')
  },
  methods: {
    modalClose (element) {
      document.querySelector(element).addEventListener('click', (e) => {
        if (document.querySelector(element) === e.target) {
          document.querySelector(element).style.visibility = 'hidden'
        }
      })
    }
  }
}
</script>

<style lang="sass">
  @import "../assets/sass/main.sass"
</style>
