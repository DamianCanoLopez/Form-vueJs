<template>
    <div id="container">
      <div class="flex items-column mb-4 ">
        <div class="timeline-container">
          <div class="spacer"></div>
          <div v-for="(step, index) in steps" :key="index" class="timeline-step">
            <div
              class="timeline-indicator"
              :class="{ 'active': currentStep === index }"
            ></div>
            <div
              class="timeline-label"
              v-text="step.label"
            ></div>
          </div>
        </div>
        <div class="w-2/3">
          <form @submit.prevent="submitForm" class="form">
            <div v-show="currentStep === 0" class="title">
              <h3 class="sub">Paso 1: Información personal</h3>
              <div class="select">
                <label for="country" class="block font-medium mb-1">País</label>
                <select id="country" v-model="formData.country" class="w-full border border-gray-300 rounded-md py-2 px-3 focus:outline-none focus:ring-2 focus:ring-blue-500 label" >
                  <option v-for="country in countries" :key="country.common" :value="country.common">{{ country.common }}</option>
                </select>
              </div>
              <div class="select">
                <label for="gender" class="block font-medium mb-1">Género</label>
                <select id="gender" v-model="formData.gender" class="w-full border border-gray-300 rounded-md py-2 px-3 focus:outline-none focus:ring-2 focus:ring-blue-500 label">
                  <option value="male">Masculino</option>
                  <option value="female">Femenino</option>
                  <option value="others">Otro</option>
                </select>
              </div>
              <div class="select">
                <label class="block mb-1 text-sm" for="first-name">Primer nombre</label>
                <input id="first-name" v-model="formData.firstName" class="w-full border px-4 py-2 rounded focus:border-blue-500 focus:shadow-outline outline-none label" type="text" autofocus placeholder="Primer nombre..." required>
              </div>
              <div class="select">
                <label class="block mb-1 text-sm" for="last-name">Segundo nombre</label>
                <input id="last-name" v-model="formData.lastName" class="w-full border px-4 py-2 rounded focus:border-blue-500 focus:shadow-outline outline-none label" type="text" autofocus placeholder="Segundo nombre..." required>
              </div>
              <div class="select">
                <label for="birthdate">Fecha de nacimiento</label>
                <input type="date" id="birthdate" v-model="formData.birthdate" class="w-full label" @change="validateBirthdate" required>
              </div>
              <div class="select">
                <label for="document-type">Tipo documento</label>
                <select id="document-type" v-model="formData.documentType" class="w-full label">
                  <option value="citizenship-card">Cédula de ciudadanía</option>
                  <option value="passport">Pasaporte</option>
                  <option value="foreign-id">Cédula de extranjería</option>
                </select>
              </div>
              <div class="select">
                <label for="document-number">Número documento</label>
                <input type="text" id="document-number" v-model="formData.documentNumber" class="w-full label" @input="validateDocumentNumber"  required>
                <span v-if="!isDocumentNumberValid">❌.</span>
                <span v-if="isDocumentNumberValid">✔</span>
              </div>
              <div class="">
                <label for="front-photo">Foto documento - Frente</label>
              <input type="file" id="front-photo" @change="handleFileUpload($event, 'frontPhoto')" class="w-full" accept="image/jpeg" required>
            </div>
            <div class="">
              <label for="back-photo">Foto documento - Reverso</label>
              <input type="file" id="back-photo" @change="handleFileUpload($event, 'backPhoto')" class="w-full" accept="image/jpeg" required>
            </div>
            <div class="flex justify-between ">
              <button @click="nextStep" class="py-2 px-4 bg-blue-500 text-white rounded button">Siguiente</button>
            </div>
          </div>
          <div v-show="currentStep === 1" class="title">
            <h3 class="sub">Paso 2: Información de contacto</h3>
            <div class="select">
              <label for="email" class="block font-medium mb-1">Correo electrónico</label>
              <input type="email" id="email" v-model="formData.email" class="w-full border border-gray-300 rounded-md py-2 px-3 focus:outline-none focus:ring-2 focus:ring-blue-500 label" @input="validateInput" required>
              <span v-if="!showError">✔</span>
              <span v-if="showError">{{ errorMessage }}</span>
            </div>
            <div class="select">
              <label for="password">Contraseña</label>
              <input type="password" id="password" v-model="formData.password" class="w-full label" @input="validatePassword" required>
            </div>
            <div class="select">
              <label for="confirm-password">Confirmación de Contraseña</label>
              <input type="password" id="confirm-password" v-model="formData.confirmPassword" class="w-full label" @input="validatePassword" required>
              <span v-if="!showErrorP">✔</span>
              <span v-if="showErrorP">❌ Las contraseñas no coinciden.</span>
            </div>
            <div class="select">
              <label for="phone">Número teléfono</label>
              <input type="tel" id="phone" v-model="formData.phoneNumber" class="w-full label">
            </div>
            <div class="select">
              <label for="cellphone">Número celular</label>
              <input type="tel" id="cellphone" v-model="formData.cellphoneNumber" class="w-full label" required>
            </div>
            <div class="flex justify-between cont-btn">
              <button @click="prevStep" class="py-2 px-4 bg-blue-500 text-white rounded button">Anterior</button>
              <button @click="nextStep" class="py-2 px-4 bg-blue-500 text-white rounded button">Siguiente</button>
            </div>
          </div>
          <div v-show="currentStep === 2" class="title last">
            <h3 class="sub">Paso 3: Dirección</h3>
            <div class="select">
              <label for="address" class="block font-medium mb-1">Dirección residencia</label>
              <input id="address" v-model="formData.address" class="w-full border border-gray-300 rounded-md py-2 px-3 focus:outline-none focus:ring-2 focus:ring-blue-500 label" required>
              <span v-if="!showAddressError">✔</span>
              <span v-if="showAddressError">❌ Ingresa una dirección válida.</span>
            </div>
            <div class="select">
              <label for="postal-code">Código postal</label>
              <input type="number" id="postal-code" v-model="formData.postalCode" class="w-full label" @input="validatePostalCode" required>
              <span v-if="!showPostalCodeError">✔</span>
              <span v-if="showPostalCodeError">❌ Ingresa un código postal válido.</span>
            </div>
            <div class="flex justify-between cont-btn">
              <button @click="prevStep" class="py-2 px-4 bg-blue-500 text-white rounded button">Anterior</button>
              <button type="submit" :disabled="!validateForm" class="py-2 px-4 bg-blue-500 text-white rounded button">Enviar</button>
            </div>
          </div>
        </form>
      </div>
    </div>

    <div v-if="modalVisible" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
        <div class="bg-white p-8 rounded sub">
          <h3 class="mb-4 text-lg font-semibold h3">¡Formulario enviado con éxito!</h3>
          <!-- <p>Los valores de cada campo del formulario son:</p>
          <pre>{{ formData }}</pre> -->
          <button @click="closeModal" class="py-2 px-4 bg-blue-500 text-white rounded button btn-close">Cerrar</button>
        </div>
      </div>
    </div>
</template>

<script>
export default {
  data() {
    return {
      steps: [
        { label: 'Paso 1' },
        { label: 'Paso 2' },
        { label: 'Paso 3' }
      ],
      currentStep: 0,
      formData: {
        country: '',
        gender: '',
        firstName: '',
        lastName: '',
        birthdate: '',
        documentType: '',
        documentNumber: '',
        frontPhoto: null,
        backPhoto: null,
        email: '',
        password: '',
        confirmPassword: '',
        phoneNumber: '',
        cellphoneNumber: '',
        address: '',
        postalCode: ''
      },
      countries: [],
      modalVisible: false
    };
  },
    computed: {
      showAddressError() {
        return this.formData.address !== '' && !/^[\w\s-.,#]+$/.test(this.formData.address);
      },
      showPostalCodeError() {
        return this.formData.postalCode !== '' && !/^\d{5}$/.test(this.formData.postalCode);
      },
      showErrorP() {
        return this.formData.confirmPassword !== '' && this.formData.confirmPassword !== this.formData.password;
      },
      showError() {
        return this.formData.email.required && !this.isInputValid;
      },
      errorMessage() {
        if (this.formData.email.type === 'email') {
          return 'Ingresa un correo electrónico válido.';
        }
        return '';
      },
      isInputValid() {
        if (this.formData.email.type === 'email') {
          return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.inputValue);
        }
        return true;
      },
      isDocumentNumberValid() {
        return /^\d{5,}$/.test(this.formData.documentNumber);
      },
    }
    ,mounted() {
    this.fetchCountries();
  },
  methods: {
    validateDocumentNumber() {
        if (!this.isDocumentNumberValid) {
          this.formData.documentNumber = this.formData.documentNumber.replace(/[^\d]/g, '').slice(0, 10);
        }
      },
    validateBirthdate() {
        const selectedDate = new Date(this.formData.birthdate);
        const currentDate = new Date();
        const eighteenYearsAgo = new Date();
        eighteenYearsAgo.setFullYear(currentDate.getFullYear() - 18);
  
        if (selectedDate > eighteenYearsAgo) {
          alert('Debes tener minimo 18 años para continuar.');
          this.formData.birthdate = '';
        }
      },
    fetchCountries() {
      fetch('https://restcountries.com/v3.1/all')
        .then(response => response.json())
        .then(data => {
          this.countries = data.map(country => country.name);
        })
        .catch(error => {
          console.error('Error al obtener la lista de países:', error);
        });
    },
    nextStep() {
      if (this.currentStep < this.steps.length - 1) {
        this.currentStep++;
      }
    },
    prevStep() {
      if (this.currentStep > 0) {
        this.currentStep--;
      }
    },
    handleFileUpload(event, field) {
      const file = event.target.files[0];
      if (file) {
        this.formData[field] = file;
      }
    },
    submitForm() {
      // Validar los campos obligatorios antes de enviar el formulario
      if (this.validateForm()) {
        // Enviar el formulario y mostrar el modal de éxito
        console.log(this.formData); // Imprimir los valores en la consola
        this.modalVisible = true;
      }
    },
    validateForm() {
      // Validar los campos obligatorios en cada paso
      switch (this.currentStep) {
        case 0:
          return (
            this.formData.country !== '' &&
            this.formData.gender !== '' &&
            this.formData.firstName !== '' &&
            this.formData.birthdate !== '' &&
            this.formData.documentType !== '' &&
            this.formData.documentNumber !== '' &&
            this.formData.frontPhoto !== null &&
            this.formData.backPhoto !== null
          );
        case 1:
          return (
            this.formData.email !== '' &&
            this.formData.password !== '' &&
            this.formData.confirmPassword !== '' &&
            this.formData.password === this.formData.confirmPassword &&
            this.formData.cellphoneNumber !== ''
          );
        case 2:
          return this.formData.address !== '' && this.formData.postalCode !== '';
        default:
          return false;
      }
    },
    closeModal() {
      this.modalVisible = false;
    }
  }
};
</script>

<style scoped>

#container{
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 2%;
  margin-bottom: 2%;
}

.timeline-container {
  display: flex;
  align-items: center;
  margin-top: 20px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.spacer {
  flex: 1;
}

.timeline-step {
  position: relative;
  flex: 2;
  display: flex;
  align-items: center;
}

.timeline-indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: #ffffff;
  margin-right: 10px;
  z-index: 1;
}

.timeline-indicator.active {
  background-color: blue;
}

.timeline-label {
  font-weight: bold;
  font-size: 12px;
  color: #666;
  text-transform: uppercase;
  z-index: 2;
}
.form {
  padding: 5%;
  padding-top: 2%;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background-color: rgb(255, 255, 255);
  border-radius: 5%;
  box-shadow: 3px 7px 10px -2px rgb(149, 50, 211);
}
.form:hover{
  box-shadow: -8px -2px 10px 7px rgb(236, 152, 42);
}

.form .title {
  font-family: 'Quicksand', sans-serif;
  font-size: 1.1rem;
  color: rgb(26, 23, 23);
}

.sub{
  font-family: 'Quicksand', sans-serif;
  text-align: center;
  font-size: 1.7rem;
  padding: 2%;
}

.select{
  display: flex;
  flex-direction: column;
}

.label{
  width:20dvw ;
  background-color: #f8eeee;
}

.button{
  width: 50%;
  position: relative;
  padding-top: 2%;
  padding-bottom: 2%;
  border-radius: 20% ;
}

.button:hover{
  box-shadow: 3px 7px 10px -2px rgb(27, 105, 127);
}

.nav{
  display: flex;
  flex-direction: row;
}

.cont-btn{
  display: flex;
  justify-content: space-between;
}

.last{
  display: flex;
  flex-direction: column;
  width: 100%;
  margin-left: 5%;
  margin-right: 5%;

}

.h3{
  color: #ffffff;
}

.btn-close{
  color: #ffffff;
}

.select input {
  width: 100%;
  box-sizing: border-box;
}

@media screen and (max-width: 768px) {
    #container {
      flex-direction: column;
    }

    .form {
      width: 100%;
    }

    .select .label {
      width: 100%;
    }

    .button {
      width: 100%;
    }
  }
</style>