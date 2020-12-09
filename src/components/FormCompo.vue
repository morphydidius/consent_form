<template lang="pug">
  .form-wrapper
    form(v-if="!isDataSent")
      label {{ fields.fio.name }}*
        input(type='text', v-model='fields.fio.content', :class="[{'invalid': isErrorInSendingData && !fields.fio.isValid}]")
      label {{ fields.email.name }}*
        input(type='email', v-model='fields.email.content', :class="[{'invalid': isErrorInSendingData && !fields.email.isValid}]")
      label {{ fields.phone.name }}*
        input(type='text', v-model='fields.phone.content', :class="[{'invalid': isErrorInSendingData && !fields.phone.isValid}]")
      label {{ fields.comment.name }}
        textarea(v-model='fields.comment.content')
      label {{ fields.isConsentGiven.name }}*
        input(type='checkbox', v-model='fields.isConsentGiven.content', :class="[{'invalid': isErrorInSendingData && !fields.isConsentGiven.isValid}]")
      div * - обязательны для заполнения
      button(@click.prevent="sendForm") Отправить
    div(v-else) Данные успешно отправлены!
</template>

<script>
export default {
  name: 'FormCompo',
  data() {
    return {
      fields: {
        fio: {
          name: 'ФИО',
          content: '',
          isValid: false,
          isRequired: true
        },
        email: {
          name: 'E-mail',
          content: '',
          isValid: false,
          isRequired: true
        },
        phone: {
          name: 'Телефон',
          content: '',
          isValid: false,
          isRequired: true
        },
        isConsentGiven: {
          name: 'Согласие на обработку персональных данных',
          content: false,
          isValid: false,
          isRequired: true
        },
        comment: {
          name: 'Комментарий',
          content: ''
        }
      },
      isErrorInSendingData: false,
      isDataSent: false
    };
  },
  computed: {
    requiredFields() {
      return Object.keys(this.fields).reduce((acc, key) => {
        if (this.fields[key].hasOwnProperty('isRequired')) {
          acc[key] = this.fields[key];
        }
        return acc;
      }, {});
    },
    fieldsWithErrors() {
      return Object.keys(this.requiredFields).filter((key) => !this.requiredFields[key].isValid);
    }
  },
  mounted() {},
  methods: {
    checkData() {
      if (this.fieldsWithErrors.length) {
        this.isErrorInSendingData = true;
      } else {
        this.isErrorInSendingData = false;
      }
    },
    generateErrorText() {
      let errorFields = '';
      let consent = '';
      let msg = '';
      this.fieldsWithErrors.forEach((field) => {
        if (field !== 'isConsentGiven') {
          errorFields = errorFields.length
            ? `${errorFields}, ${this.fields[field].name}`
            : `${this.fields[field].name}`;
        } else {
          consent = 'Для отправки данных необходимо дать согласие на их обработку';
        }
      });
      if (errorFields.length) {
        msg = `Заполните обязательные поля: ${errorFields}`;
        if (consent.length) {
          msg = `${msg}\n${consent}`;
        }
      } else {
        msg = consent;
      }
      return msg;
    },
    sendForm() {
      this.checkData();
      if (this.isErrorInSendingData) {
        const message = this.generateErrorText();
        alert(message);
      } else {
        this.isDataSent = true;
      }
    }
  },
  watch: {
    'fields.fio.content'() {
      this.fields.fio.isValid = !!this.fields.fio.content.length;
    },
    'fields.email.content'() {
      this.fields.email.isValid = !!this.fields.email.content.length;
    },
    'fields.phone.content'() {
      this.fields.phone.isValid = !!this.fields.phone.content.length;
    },
    'fields.isConsentGiven.content'() {
      this.fields.isConsentGiven.isValid = !!this.fields.isConsentGiven.content;
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

  .invalid {
    border-color: red;
  }
}
</style>
