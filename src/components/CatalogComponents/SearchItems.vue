<template>
  <div class="searchContainer">
    <div class="searchItems">
      <input
        class="inputCarName"
        type="text"
        placeholder="Buscar.."
        v-model="searchTerm"
        @input="emitInput"
      />
    </div>
    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'car']" class="icono-marca" />
      <select v-model="brand" @change="inputBrand" class="selects">
        <option value="">Marca</option>
        <option v-for="brand in brands" :key="brand">{{ brand }}</option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>

    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'warehouse']" class="icono-marca" />
      <select v-model="model" @change="inputModel" class="selects">
        <option value="">Modelo</option>
        <option v-for="model in models" :key="model">{{ model }}</option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>

    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'calendar-days']" class="icono-marca" />
      <select v-model="year" @change="inputYear" class="selects">
        <option value="">Año</option>
        <option v-for="yearOption in yearOptions" :key="yearOption" :value="yearOption">
          {{ yearOption }}
        </option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>

    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'gas-pump']" class="icono-marca" />
      <select v-model="fuel" @change="inputFuel" class="selects">
        <option value="">Combustible</option>
        <option value="Gasolina">Gasolina</option>
        <option value="Diésel">Diesel</option>
        <option value="Eléctrico">Eléctrico</option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>

    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'screwdriver-wrench']" class="icono-marca" />
      <select v-model="transmission" @change="inputTransmission" class="selects">
        <option value="">Transmisión</option>
        <option value="Manual">Manual</option>
        <option value="Automatico">Automático</option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>
    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'shield']" class="icono-marca" />
      <select v-model="airbag" @change="inputAirbag" class="selects">
        <option value="">Airbag</option>
        <option value="yes">Si</option>
        <option value="no">No</option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>
    <div class="grupo">
      <font-awesome-icon :icon="['fas', 'location-dot']" class="icono-marca" />
      <select v-model="region" @change="inputRegion" class="selects">
        <option value="">Región</option>
        <option value="Antofagasta">Antofagasta</option>
        <option value="Araucanía">Araucanía</option>
        <option value="Arica y Parinacota">Arica y Parinacota</option>
        <option value="Atacama">Atacama</option>
        <option value="Aysén del General Carlos Ibáñez del Campo">Aysén</option>
        <option value="Biobío">Biobío</option>
        <option value="Coquimbo">Coquimbo</option>
        <option value="Libertador General Bernardo O'Higgins">Bernardo O'Higgins</option>
        <option value="Los Lagos">Los Lagos</option>
        <option value="Los Ríos">Los Ríos</option>
        <option value="Magallanes y de la Antártica Chilena">Magallanes</option>
        <option value="Maule">Maule</option>
        <option value="Metropolitana de Santiago">Metropolitana</option>
        <option value="Tarapacá">Tarapacá</option>
        <option value="Valparaíso">Valparaíso</option>
        <option value="Ñuble">Ñuble</option>
      </select>
      <font-awesome-icon :icon="['fas', 'chevron-down']" class="icono-chevron" />
    </div>

    <div class="grupo">
      <div class="price-filter-container">
        <div class="price-label">Precio</div>
        <div class="price-inputs-container">
          <input
            type="text"
            class="price-input"
            placeholder="Min."
            v-model="minPrice"
            @input="formatPriceInput('minPrice')"
            @blur="updateSelectedMinPrice(minPrice)"
            @change="$emit('inputMinPrice', minPrice)"
          />
          <div class="price-separator">a</div>
          <input
            type="text"
            class="price-input"
            placeholder="Max."
            v-model="maxPrice"
            @input="formatPriceInput('maxPrice')"
            @blur="updateSelectedMaxPrice(maxPrice)"
            @change="$emit('inputMaxPrice', maxPrice)"
          />
        </div>
      </div>
    </div>

    <div class="grupo">
      <div class="price-filter-container">
        <div class="price-label">Kilometraje</div>
        <div class="price-inputs-container">
          <input type="text" class="price-input" placeholder="Max." v-model="mileage" />
          <button class="btnAplicar" @click="emitInput">Aplicar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
export default {
  components: {
    FontAwesomeIcon
  },
  data() {
    return {
      isLoading: true,
      searchTerm: '',
      brands: [],
      models: [],
      brand: '',
      model: '',
      year: '',
      fuel: '',
      price: '',
      transmission: '',
      region: '',
      minPrice: '',
      maxPrice: '',
      mileage: '',
      airbag: '',
      yearOptions: []
    }
  },
  created() {
    const anioActual = new Date().getFullYear()
    for (let i = anioActual; i >= 1900; i--) {
      this.yearOptions.push(i.toString())
    }
    this.getBrands()
  },

  methods: {
    emitInput() {
      this.$emit('inputKM', this.mileage)
      this.$emit('inputItems', this.searchTerm)
    },
    inputBrand() {
      if (this.brand === '') {
        this.model = ''
        this.models = ''
      } else {
        this.getModels(this.brand)
      }
      this.$emit('inputBrand', this.brand)
    },
    inputAirbag() {
      this.$emit('inputAirbag', this.airbag)
    },
    inputModel() {
      if (this.model === null || this.model === '') {
        this.$emit('showFullCatalog');
      }
      this.$emit('inputModel', this.model);
    },
    inputTransmission() {
      this.$emit('inputTransmission', this.transmission)
    },
    inputYear() {
      this.$emit('inputYear', this.year)
    },
    inputFuel() {
      this.$emit('inputFuel', this.fuel)
    },
    inputRegion() {
      this.$emit('inputRegion', this.region)
    },
    inputKM() {
      this.$emit('inputKM', this.mileage)
    },
    updatePrice() {
      this.price = [this.formatPriceInput(this.minPrice), this.formatPriceInput(this.maxPrice)]
      this.$emit('inputPrice', this.price)
    },
    formatPriceInput(priceType) {
      let value = this[priceType].replace(/[\D]/g, '')
      value = parseInt(value, 10)
      if (isNaN(value)) {
        value = ''
      } else {
        value = value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.') // Para colocar puntos chavales
      }
      this[priceType] = `$${value}` // añadir el signo de dolar
      this.$emit('priceChange', { minPrice: this.minPrice, maxPrice: this.maxPrice })
    },
    handlePriceChange(minPrice, maxPrice) {
      this.price = [minPrice, maxPrice]
    },
    async getBrands() {
      try {
        const response = await axios.get('http://localhost:8080/brands')
        this.brands = response.data
      } catch (error) {
        console.error(error)
      }
    },
    async getModels(brand) {
      try {
        const response = await axios.get('http://localhost:8080/models/' + brand)
        this.models = response.data
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>

<style>

.btnAplicar {
  background-color: #fbc40e;
  width: 45%;
  color: black;
  padding: 10px 0px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  box-sizing: border-box;
  font-weight: 600;
}

.btnAplicar:hover {
  background-color: #c19400;
}

.price-separator {
  color: #fff;
}
.price-inputs-container {
  display: flex;
  justify-content: space-between;
}

.price-input,
.selects {
  box-sizing: border-box;
  width: calc(50% - 12px);
  padding: 10px;
  margin: 0;
  border: none;
}

.price-input {
  background-color: #fff;
  padding: 10px;
  border-radius: 5px;
  border: none;
}

.price-input:hover {
  background-color: #d1d1d1;
}

input:focus {
  outline: none;
}

input[type='number']::-webkit-inner-spin-button,
input[type='number']::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.price-input:last-child {
  margin-right: 0;
}

.price-separator {
  display: inline-block;
  width: auto;
  margin: 0 5px;
  align-self: center;
}

.price-label {
  margin-bottom: 10px;
  color: #fff;
}

.icono-chevron {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  pointer-events: none;
  color: #fff;
}

.searchContainer {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #1a1a1a;
}

.searchItems {
  width: 90%;
  margin-bottom: 20px;
  margin-top: 20px;
}

.inputCarName {
  box-sizing: border-box;
  width: 100%;
  padding: 10px;
  font-size: 15px;
  border: none;
  border-radius: 4px 4px 2px 2px;
  background-color: white;
  outline: none;
  margin: 0;
}

.input {
  max-width: 190px;
  background-color: #f5f5f5;
  color: #242424;
  padding: 0.15rem 0.5rem;
  min-height: 40px;
  border-radius: 4px;
  outline: none;
  border: none;
  line-height: 1.15;
  box-shadow: 0px 10px 20px -18px;
}

input:focus {
  border-bottom: 4px solid #fbc40e;
  border-radius: 4px 4px 2px 2px;
}

.iconos {
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: #fbc40e;
  z-index: 10;
}

.grupo {
  width: 90%;
  position: relative;
  margin-bottom: 20px;
}

.selects {
  width: 100%;
  padding: 10px 10px 10px 40px;
  font-size: 15px;
  background-color: #1a1a1a;
  color: white;
  border: none;
  outline: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}

.grupo:hover .selects {
  color: #fbc40e;
  cursor: pointer;
}

.grupo:hover .icono-chevron {
  color: #fbc40e;
  cursor: pointer;
}

.selects option {
  background-color: #272727;
  color: white;
}

.icono-marca {
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: #fbc40e;
  z-index: 10;
}
</style>
