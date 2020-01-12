<template>
  <ValidationProvider ref="form">
    <form id="form" action="#" class="form" method="POST" @submit.prevent="checkForm">
      <h1>İletişim Formu</h1>
      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="form__group" :class="{'form__group--has-error' : errors.length > 0}">
          <label for="name" class="form__label">İsim Soyisim*</label>
          <input id="name" v-model="forms.name" name="name" type="text" class="form__control"/>
          <div class="form__err">{{ errors[0] }}</div>
        </div>
      </ValidationProvider>

      <ValidationProvider rules="required|email" v-slot="{ errors }">
        <div class="form__group" :class="{'form__group--has-error' : errors.length > 0}">
          <label for="email" class="form__label">E-Mail*</label>
          <input id="email" v-model="forms.email" name="email" type="text" class="form__control"/>
          <div class="form__err">{{ errors[0] }}</div>
        </div>
      </ValidationProvider>

      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="form__group" :class="{'form__group--has-error' : errors.length > 0}">
          <label for="phone" class="form__label">Telefon Numarası*</label>
          <VuePhoneNumberInput v-model="forms.phone" default-country-code="TR" :translations="{
            phoneNumberLabel: '',
            countrySelectorLabel: 'Ülke kodu',
            example: 'Örnek :'}" size="lg" :required="true" :border-radius="0" :countries-height="50" :show-code-on-list="true" color="none" error-color="#ff5868" :clearable="true" @update="phoneUpdate"/>
            <input id="phone" v-model="forms.phone" name="name" type="text" class="form__control form__hidden"/>
          <div class="form__err">{{ errors[0] }}</div>
        </div>
      </ValidationProvider>
      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="form__group" :class="{'form__group--has-error' : errors.length > 0}">
          <label for="date" class="form__label">Tarih*</label>
            <datepicker :language="tr" rules="required" v-slot="{ errors }" v-model="forms.date" :value="forms.date" name="date" input-class="form__control js-datepicker"></datepicker>
            <input id="date" v-model="forms.date" name="date" type="text" class="form__control form__hidden"/>
          <div class="form__err">{{ errors[0] }}</div>
        </div>
      </ValidationProvider>

      <div class="form__group">
        <label for="message" class="form__label">Mesaj</label>
        <textarea id="message" v-model="forms.message" cols="30" rows="10" class="form__control"></textarea>
      </div>

      <ValidationProvider rules="checked" v-slot="{ errors }">
        <div class="form__group" :class="{'form__group--has-error' : errors.length > 0}">
          <label class="checkbox">
            <input type="checkbox" id="agreement" v-model="forms.agreement">
            <span class="checkbox__outside">
              <div class="checkbox__inside">
                <i class="icon-check"></i>
              </div>
            </span>
            Kullanıcı sözleşmesi*
          </label>
          <div class="form__err">{{ errors[0] }}</div>
        </div>
      </ValidationProvider>
      <div class="form__group">
        <button type="submit" class="form__btn" :disabled="this.buttonCheck == true" v-on:keyup.enter="submit">Gönder</button>
      </div>
    </form>
    <Modal v-if="randomModal !== null" :modalType="randomModal" :modalDetail="randomModal == 1 ? forms : {}"/>
  </ValidationProvider>
</template>

<script>
import { ValidationProvider, extend } from 'vee-validate'
import { required, email } from 'vee-validate/dist/rules'
import { en, tr } from 'vuejs-datepicker/dist/locale'
import Datepicker from 'vuejs-datepicker'
import VuePhoneNumberInput from 'vue-phone-number-input'
import 'vue-phone-number-input/dist/vue-phone-number-input.css'

import Modal from '../components/Modal'

extend('required', {
  ...required,
  message: 'Bu alanın doldurulması zorunludur.',
  computesRequired: true
})

extend('email', {
  ...email,
  message: 'Email adresi geçersiz.'
})

extend('checked', {
  validate: agreement => {
    return agreement === true
  },
  message: 'Kullanıcı sözleşmesini kabul edin.'
})

export default {
  name: 'Form',
  components: {
    ValidationProvider,
    VuePhoneNumberInput,
    Datepicker,
    Modal
  },
  data () {
    return {
      forms: {
        name: null,
        email: null,
        phone: null,
        date: null,
        agreement: false,
        message: null
      },
      en: en,
      tr: tr,
      randomModal: null,
      results: null
    }
  },
  mounted () {
    document.querySelector('.input-tel__input').addEventListener('focusout', () => {
      if (this.results === null || this.results.isValid === false) {
        document.querySelectorAll('[for="phone"]')[0].click()
        document.getElementById('phone').blur()
      }
    })

    document.querySelector('.js-datepicker').addEventListener('focusout', () => {
      document.querySelectorAll('[for="date"]')[0].click()
      document.getElementById('date').blur()
    })
  },
  created () {
    //
  },
  computed: {
    buttonCheck () {
      if (this.forms.name && this.forms.email && this.forms.phone && this.forms.date && this.forms.agreement && this.results.isValid) {
        return false
      } else {
        return true
      }
    }
  },
  methods: {
    checkForm () {
      let formDom = this.$refs.form
      console.log(this)
      formDom.validate().then(success => {
        this.randomModal = parseInt(Math.random() * 2)
        // this.forms.name = this.forms.email = this.forms.phone = this.forms.date = this.forms.message = this.results = null
        // this.forms.agreement = false
        this.$nextTick(() => {
          this.$refs.form.reset()
        })
        this.modalShow('.modal')
        // document.querySelector('.form').reset()
      })
    },
    modalShow (element) {
      if (document.querySelector('.modal') && document.querySelector(element).style.visibility === 'hidden') {
        document.querySelector(element).style.visibility = 'visible'
      }
    },
    phoneUpdate (payload) {
      this.results = payload
    }
  }
}
</script>

<style lang="sass">
  @import "../assets/sass/main.sass"
</style>
