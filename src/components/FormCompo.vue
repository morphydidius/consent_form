<template lang="pug">
  .form-wrapper
    form(ref='form', v-if="!isDataSent")
      label 
        span {{ fields.fio.name }} *
        input(type='text', v-model='fields.fio.content', :class="[{'invalid': isDataWrong && !fields.fio.isValid}]")
      label
        span {{ fields.email.name }} *
        input(type='email', v-model='fields.email.content', :class="[{'invalid': isDataWrong && !fields.email.isValid}]")
      label
        span {{ fields.phone.name }} *
        input(type='tel', v-model='fields.phone.content', :class="[{'invalid': isDataWrong && !fields.phone.isValid}]")
      label.comment
        span {{ fields.comment.name }}
        textarea(v-model='fields.comment.content')
      label.consent(:class="[{'checked': fields.isConsentGiven.content}]")
        span {{ fields.isConsentGiven.name }} *
        input(type='checkbox', v-model='fields.isConsentGiven.content', :class="[{'invalid': isDataWrong && !fields.isConsentGiven.isValid}]")
      div.info * - обязательны для заполнения
      button(type="submit") Отправить
      div.error(v-if="isErrorInSendingData") Ошибка отправки данных ;(
    div.success(v-else) Данные успешно отправлены!
</template>

<script>
import 'whatwg-fetch';

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
      isDataWrong: false,
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
  mounted() {
    this.$refs.form.addEventListener('submit', this.sendForm);
  },
  methods: {
    checkData() {
      if (this.fieldsWithErrors.length) {
        this.isDataWrong = true;
      } else {
        this.isDataWrong = false;
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
    async sendForm(e) {
      e.preventDefault();
      this.checkData();
      if (this.isDataWrong) {
        const message = this.generateErrorText();
        alert(message);
      } else {
        await fetch('https://httpbin.org/post', {
          method: 'POST',
          body: JSON.stringify({
            fio: this.fields.fio.content,
            email: this.fields.email.content,
            phone: this.fields.phone.content,
            comment: this.fields.comment.content,
            consent: this.fields.isConsentGiven.content
          }),
          headers: {'Content-Type': 'application/json'}
        })
        .then((result) => {
          if (result.status === 200) {
            this.isDataSent = true;
            this.isErrorInSendingData = false;
          } else {
            this.isErrorInSendingData = true;
          }
        })
        .catch((e) => {
          console.log(e);
          this.isErrorInSendingData = true;
        })
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
  },
  beforeDestroy() {
    if (this.$refs.form) {
      this.$refs.form.removeEventListener('submit', this.sendForm);
    }
  }
}
</script>

<style lang="scss" scoped>
.form-wrapper {
  display: flex;
  font: 16px/24px 'Arial';

  form {
    margin: 40px auto;
    width: 500px;
  }

  label {
    display: flex;
    align-items: center;
    margin-bottom: 16px;

    &.comment {
      flex-direction: column;
      align-items: flex-start;

      span {
        margin-bottom: 12px;
      }
    }

    & > span {
      display: block;
      min-width: 120px;
    }

    input {
      border-radius: 20px;
      box-shadow: none;
      border-width: 3px;
      border-color: #3bb005;
      border-top: 3px solid #3bb005;
      border-left: 3px solid #3bb005;
      border-right: 3px solid #3bb005;
      border-bottom: 3px solid #3bb005;
      padding: 6px 20px;

      &:active, &:focus {
        outline: none;
      }
    }

    input, textarea {
      width: 100%;
    }

    textarea {
      max-width: 100%;
      min-height: 100px;
      border: 3px solid #3bb005;
      border-radius: 10px;
      padding: 6px 20px;
      box-sizing: border-box;
    }    
  }

  .consent {
    justify-content: space-between;
    position: relative;

    &::after {
      content: '\2713';
      position: absolute;
      width: 25px;
      height: 25px;
      top: 0;
      right: 0;
      border: 2px solid #3bb005;
      font: 20px/24px 'Arial';
      text-align: center;
      box-sizing: border-box;
      background-color: #fff;
      color: #fff;
    }

    &.checked {
      &::after {
        background-color: #3bb005;
      }
    }

    input[type='checkbox'] {
      display: none;
    }
  }

  .invalid {
    border-color: red;
  }

  .info {
    font: italic 14px/19px 'Arial';
    margin-bottom: 20px;
  }

  button[type='submit'] {
    font: bold 16px/16px 'Arial';
    color: #fff;
    background-color: #3bb005;
    border-radius: 20px;
    padding: 6px 20px;
    border: 3px solid #3bb005;
    transition: all .2s ease-in-out;

    &:hover {
      background-color: #e66d17;
      border-color: #e66d17;
    }

    &:active, &:focus {
      outline: none;
    }
  }

  .error {
    margin-top: 20px;
  }

  .success {
    text-align: center;
    padding: 20px;
    width: 100%;
  }
}
</style>
