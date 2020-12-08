<template lang="pug">
  .form-wrapper
    form(ref='form')
      label ФИО
        input(type='text', v-model='fields.fio.content')
      label E-mail
        input(type='email', v-model='fields.email.content')
      label Телефон
        input(type='text', v-model='fields.phone.content')
      label Комментарий
        textarea(v-model='fields.comment.content')
      label Согласие на обработку персональных данных
        input(type='checkbox', v-model='fields.isConsentGiven.content')
      button(@click.prevent="sendForm") Отправить
</template>

<script>
export default {
  name: 'FormCompo',
  data() {
    return {
      fields: {
        fio: {
          content: '',
          // isValid: !!this.content.length,
          isRequired: true
        },
        email: {
          content: '',
          // isValid: !!fields.email.content.length,
          isRequired: true
        },
        phone: {
          content: '',
          // isValid: !!fields.phone.content.length,
          isRequired: true
        },
        isConsentGiven: {
          content: false,
          // isValid: !!fields.fio.content,
          isRequired: true
        },
        comment: {
          content: ''
        }
      }
    };
  },
  computed: {
    fieldsKeys() {
      return Object.keys(this.fieldsState);
    },
    fieldsState() {
      return Object.keys(this.fields).reduce((acc, key) => {
        acc[key] = this.fields[key];
        if (key === 'comment') {
          return acc;
        }
        // console.log('acc', acc);
        acc[key].isValid = this.checkValid(key);
        return acc;
      }, {});
    },
    requiredFields() {
      return this.fieldsKeys.reduce((acc, key) => {
        if (this.fields[key].hasOwnProperty('isRequired')) {
          acc[key] = this.fields[key];
        }
        return acc;
      }, {});
    },
    fieldsWithErrors() {
      return Object.keys(this.requiredFields).filter((key) => !this.fieldsState[key].isValid);
    }
  },
  mounted() {
    // this.$refs.form.addEventListener('submit', this.sendForm);
    // console.log(this.fields);
  },
  methods: {
    checkValid(key) {
      if (key === 'isConsentGiven') {
        return this.fields.isConsentGiven.content;
      }
      return !!this.fields[key].content.length;
    },
    sendForm() {
      // e.preventDefault();
      console.log('errors', this.fieldsWithErrors);
      // alert(this.errors);
    }
  },
  beforeDestroy() {
    // this.$refs.form.removeEventListener('submit', this.sendForm);
  },
  watch: {
    'required.fio'() {
      if (this.required.fio.length) {
        if (this.errors.includes('fio')) {
          delete this.errors['fio'];
          console.warn(this.errors);
        }
      }
    },
    'required.email'() {
      if (this.required.email.length) {
        if (this.errors.includes('email')) {
          delete this.errors['email'];
          console.warn(this.errors);
        }
      }
    },
    'required.phone'() {
      if (this.required.phone.length) {
        if (this.errors.includes('phone')) {
          delete this.errors['phone'];
          console.warn(this.errors);
        }
      }
    },
    'required.isConsentGiven'() {
      if (this.required.isConsentGiven.length) {
        if (this.errors.includes('isConsentGiven')) {
          delete this.errors['isConsentGiven'];
          console.warn(this.errors);
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.form-wrapper {
  display: flex;
  label {
    display: block;
  }
}
</style>
