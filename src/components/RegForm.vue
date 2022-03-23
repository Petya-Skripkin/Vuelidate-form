<template>
  <form @submit.prevent="onSubmit">
    <div v-show="step === 1" class="step">
      <label>
        Фамилия:*
        <input
          @blur="$v.sername.$touch"
          type="text"
          v-model="sername"
          :class="{ input__error: $v.sername.$error }"
        />
      </label>
      <div class="error" v-if="$v.sername.$error">
        Поле должно быть заполненно
      </div>
      <label>
        Имя:*
        <input
          @blur="$v.name.$touch"
          type="text"
          v-model="name"
          :class="{ input__error: $v.name.$error }"
        />
      </label>
      <div class="error" v-if="$v.name.$error">Поле должно быть заполненно</div>
      <label>
        Отчество:
        <input type="text" v-model.trim="patronymic" />
      </label>
      <label>
        Номер телефона:*
        <input
          type="text"
          v-model.trim="phoneNumber"
          :class="{ input__error: $v.phoneNumber.$error }"
          @blur="$v.phoneNumber.$touch"
        />
      </label>
      <div
        class="error"
        v-if="$v.phoneNumber.$error && !$v.phoneNumber.required"
      >
        Поле должно быть заполнено
      </div>
      <div class="error" v-if="!$v.phoneNumber.numeric">
        Ввод букв и символов запрещен
      </div>
      <div class="error" v-else-if="!$v.phoneNumber.minLength">
        Должно быть не менее 11 цифр
      </div>
      <div class="error" v-if="!$v.phoneNumber.maxLength">
        Должно быть не более 13 цифр
      </div>
      <label>
        Дата рождения:*
        <input
          v-model.trim="birthDate"
          type="date"
          :class="{ input__error: $v.birthDate.$error }"
          @blur="$v.birthDate.$touch"
        />
      </label>
      <div class="error" v-if="$v.birthDate.$error">
        Поле должно быть заполненно
      </div>
      <label>
        Пол:
        <select class="input" v-model.trim="gender">
          <option
            class="input"
            v-for="(client, i) of genders"
            :key="i"
            v-bind:value="client"
          >
            {{ client }}
          </option>
        </select>
      </label>
      <label>
        Группа клиентов:*
        <select
          class="input"
          v-model.trim="selectedUser"
          :class="{ input__error: $v.selectedUser.$error }"
        >
          <option
            class="input"
            v-for="(client, i) of clientGroup"
            :key="i"
            v-bind:value="client"
          >
            {{ client }}
          </option>
        </select>
      </label>
      <div
        class="error"
        v-if="$v.selectedUser.$error && !$v.selectedUser.required"
      >
        Необходимо выбрать группу
      </div>
      <label>
        Лечащий врач:
        <select class="input" v-model.trim="selectedDoctor">
          <option
            class="input"
            v-for="(doctor, i) of doctors"
            :key="i"
            v-bind:value="doctor"
          >
            {{ doctor }}
          </option>
        </select>
      </label>
      <button type="button" @click="nextStep" :disabled="disabledBtn1">Далее</button>
      <label class="checkbox">
        <input type="checkbox" name="sms" v-model.trim="sms" />
        Не отправлять СМС
      </label>
    </div>
    <div v-show="step === 2" class="step">
      <label>
        Индекс:
        <input
          @blur="$v.adress.index.$touch"
          type="text"
          v-model="adress.index"
          :class="{ input__error: !$v.adress.index.integer }"
        />
      </label>
      <div class="error" v-if="!$v.adress.index.integer">
        Символы или буквы вводить запрещено
      </div>
      <label>
        Страна:
        <input
          @blur="$v.adress.country.$touch"
          type="text"
          v-model="adress.country"
          :class="{ input__error: !$v.adress.country.alpha }"
        />
      </label>
      <div class="error" v-if="!$v.adress.country.alpha">
        Ввод цыфр запрещен
      </div>
      <label>
        Область:
        <input
          @blur="$v.adress.region.$touch"
          type="text"
          v-model="adress.region"
          :class="{ input__error: !$v.adress.region.alpha }"
        />
      </label>
      <div class="error" v-if="!$v.adress.region.alpha">Ввод цыфр запрещен</div>
      <label>
        Город:*
        <input
          @blur="$v.adress.city.$touch"
          type="text"
          v-model="adress.city"
          :class="{ input__error: $v.adress.city.$error }"
        />
      </label>
      <div
        class="error"
        v-if="$v.adress.city.$error && !$v.adress.city.required"
      >
        Поле должно быть заполненно
      </div>
      <div class="error" v-if="!$v.adress.city.alpha">Ввод цыфр запрещен</div>
      <label>
        Улица:
        <input
          @blur="$v.adress.street.$touch"
          type="text"
          v-model="adress.street"
          :class="{ input__error: $v.adress.street.$error }"
        />
      </label>
      <div class="error" v-if="!$v.adress.street.alpha">Ввод цыфр запрещен</div>
      <label>
        Дом:
        <input type="text" v-model.trim="$v.adress.home" />
      </label>
      <div class="buttons">
        <button type="button" @click="prevStep" class="prev">Назад</button>
        <button type="button" @click="nextStep" :disabled="disabledBtn2">Далее</button>
      </div>
    </div>
    <div v-show="step === 3" class="step">
      <label>
        Номер:
        <input
          type="text"
          v-model="pasport.number"
          :class="{ input__error: $v.pasport.number.$error }"
        />
      </label>
      <div class="error" v-if="!$v.pasport.number.numeric">
        Вводить символы или буквы запрещено
      </div>
      <label>
        Серия:
        <input type="text" v-model="pasport.series" />
      </label>
      <label>
        Кем выдан:
        <input
          type="text"
          v-model.trim="$v.pasport.issuedBy.$model"
          :class="{ input__error: $v.pasport.issuedBy.$error }"
        />
      </label>
      <div class="error" v-if="!$v.pasport.issuedBy.alpha">
        Ввод цифр запрещен
      </div>
      <label>
        Дата выдачи:*
        <input
          v-model.trim="$v.pasport.date.$model"
          type="date"
          :class="{ input__error: $v.pasport.date.$error }"
          @blur="$v.pasport.date.$touch"
        />
      </label>
      <div
        class="error"
        v-if="!$v.pasport.date.required && $v.pasport.date.$error"
      >
        Поле должно быть заполненно
      </div>
      <label>
        Тип документа:*
        <select
          class="input"
          v-model.trim="pasport.selectCard"
          :class="{ input__error: $v.pasport.selectCard.$error }"
          @blur="$v.pasport.selectCard.$touch"
        >
          <option
            class="input"
            v-for="(idcard, i) of pasport.idcards"
            :key="i"
            v-bind:value="idcard"
          >
            {{ idcard }}
          </option>
        </select>
      </label>
      <div
        class="error"
        v-if="!$v.pasport.selectCard.required && $v.pasport.selectCard.$error"
      >
        Поле должно быть заполненно
      </div>
      <div class="buttons">
        <button type="button" @click="prevStep" class="prev">Назад</button>
        <button type="submit" :disabled="disabledBtn3">Зарегестрировать</button>
      </div>
    </div>
  </form>
</template>


<script>
import {
  required,
  numeric,
  minLength,
  maxLength,
  integer,
  helpers,
} from "vuelidate/lib/validators";

const alpha = helpers.regex("alpha", /^\D*$/);

export default {
  data() {
    return {
      step: 1,
      name: "",
      sername: "",
      patronymic: "",
      birthDate: "",
      phoneNumber: "",
      gender: "",
      genders: ["Мужской", "Женский"],
      selectedUser: "",
      clientGroup: ["VIP", "Проблемные", "ОМС"],
      selectedDoctor: "",
      doctors: ["Иванов", "Захаров", "Чернышева"],
      sms: false,
      adress: {
        index: "",
        country: "",
        region: "",
        city: "",
        street: "",
        home: "",
      },
      pasport: {
        selectCard: "",
        idcards: ["Паспорт", "Свидетельство о рождении", "Вод. удостоверение"],
        series: "",
        number: "",
        issuedBy: "",
        date: "",
      },
    };
  },
  computed: {
    disabledBtn1() {
      return (
        this.$v.sername.$invalid ||
        this.$v.name.$invalid ||
        this.$v.birthDate.$invalid ||
        this.$v.phoneNumber.$invalid ||
        this.$v.selectedUser.$invalid
      );
    },
    disabledBtn2() {
      return (
        this.$v.adress.city.$invalid
      );
    },
    disabledBtn3() {
      return (
        this.$v.pasport.selectCard.$invalid ||
        this.$v.pasport.date.$invalid
      );
    },
  },
  methods: {
    nextStep() {
      if (this.step < 3) {
        this.step++;
      }
    },
    prevStep() {
      if (this.step > 1) {
        this.step--;
      }
    },
    onSubmit() {
      const value = {
        name: this.name,
        sername: this.sername,
        patronymic: this.patronymic,
        birthDate: this.birthDate,
        phoneNumber: this.phoneNumber,
        gender: this.gender,
        selectedUser: this.selectedUser,
        selectedDoctor: this.selectedDoctor,
        sms: this.sms,
        adress: this.adress,
        pasport: this.pasport,
      };

      alert(`Пользователь ${value.name} ${value.sername} зарегестрирован!`)

      // step 1 data clear
      this.name = "";
      this.sername = "";
      this.patronymic = "";
      this.birthDate = "";
      this.phoneNumber = "";
      this.gender = "";
      this.selectedUser = "";
      this.selectedDoctor = "";
      this.sms = false;
      // step 2 data clear
      this.adress.index = "";
      this.adress.country = "";
      this.adress.region = "";
      this.adress.city = "";
      this.adress.street = "";
      this.adress.home = "";
      // step 3 data clear
      this.pasport.selectCard = "";
      this.pasport.series = "";
      this.pasport.number = "";
      this.pasport.issuedBy = "";
      this.pasport.date = "";

      this.$v.$reset();
      this.step = 1;
    },
  },
  validations: {
    name: {
      required,
    },
    sername: {
      required,
    },
    birthDate: {
      required,
    },
    phoneNumber: {
      required,
      numeric,
      minLength: minLength(11),
      maxLength: maxLength(13),
    },
    selectedUser: {
      required,
    },
    adress: {
      index: {
        integer,
      },
      country: {
        alpha,
      },
      region: {
        alpha,
      },
      city: {
        required,
        alpha,
      },
      street: {
        alpha,
      },
    },
    pasport: {
      number: {
        numeric,
      },
      issuedBy: {
        alpha,
      },
      date: {
        required,
      },
      selectCard: {
        required,
      },
    },
  },
};
</script>

<style scoped>
.text {
  padding: 5px 2px;
  border-radius: 5px;
  border-color: green;
}
input {
  width: auto;
  font-size: 22px;
  padding: 2px 5px;
  border: 1px solid green;
  border-radius: 5px;
  outline: none;
}

input:nth-last-of-type(1) {
  width: auto;
}

.input {
  width: 30%;
  font-size: 22px;
  padding: 2px 5px;
  border: 1px solid green;
  border-radius: 5px;
  outline: none;
}

.input__error {
  border: 1px solid red;
}

.error {
  flex: 1;
  color: red;
  text-align: center;
}

label {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 5px;
  margin-top: 15px;
}

.checkbox {
  flex-direction: row;
  justify-content: center;
  margin: 10px 0;
}

form {
  padding: 0 25%;
}

button {
  padding: 10px 20px;
  margin-top: 20px;
  background: lightgreen;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.prev {
  background: rgb(192, 192, 192);
}

.buttons {
  display: flex;
  justify-content: space-around;
}
</style>