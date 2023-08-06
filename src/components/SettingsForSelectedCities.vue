<template>
<div class="settings">
  <div class="settings__header">
    <div class="settings__title">
      Settings
    </div>
    <div class="settings__close">
      <!--  icon by Icons8-->
      <img
          src="/icon/close.svg"
          alt="icon"
          @click="$emit('close')"
      />
    </div>
  </div>
  <div
      v-if="cities.length > 0"
      class="settings__selected-cities"
  >
    <div
        v-for="(city, index) in cities"
        :key="city.name + index"
        draggable="true"
        @drag="drag(index)"
        @dragover.prevent
        @drop.stop="drop(index)"
        class="settings__city"
    >
      <div class="settings__hamburger">
        <img
            src="/icon/hamburger.svg"
            alt="hamburger"
        />
      </div>
      <div>{{city.name}}</div>
      <div class="settings__delete">
        <img
            src="/icon/delete.svg"
            alt="delete"
            @click="deleteCity(index)"
        />
      </div>
    </div>

  </div>
  <div class="settings__input">
    <input
        v-model="city"
        type="text"
        placeholder="Enter city name"
        @keyup.enter="addCity"
    />
    <button @click="addCity">Add</button>
  </div>

</div>
</template>

<script>
export default {
  name: "SettingsForSelectedCities",
  emits: ['close', 'update-cities'],
  data() {
    return {
      cities: [],
      city: '',
      draggedItem: null
    }
  },
  mounted() {
    this.load()
  },
  methods: {
    load() {
      try {
        this.cities = JSON.parse(localStorage.getItem('cities')) || []
      } catch (e) {
        localStorage.removeItem('cities')
      }
    },
  /**
   * @description method for adding a city to the list of selected cities
   */
    addCity() {
      if (this.city) {
        if(!this.cities.find(item => item.name === this.city)) {
          this.cities.push({
            name: this.city
          })
        }

        this.$emit('update-cities', this.cities)
        this.save()
        this.city = ''
      }
    },

    /**
     * @description  drag and drop cities
     */
    drag(index) {
      this.draggedItem = index;
    },
    drop( index) {
      this.cities.splice(index, 0, this.cities.splice(this.draggedItem, 1)[0]);
      this.save()
      this.$emit('update-cities', this.cities)
    },

    /**
     * @description  delete city from list
     */
    deleteCity(index) {
      this.cities.splice(index, 1)
      this.save()
      this.$emit('update-cities', this.cities)
    },
    /**
     * @description  save method in localstorage
     */
    save() {
      localStorage.setItem('cities', JSON.stringify(this.cities))
    }

  }
}
</script>

<style scoped lang="scss">
.settings {
  width: 200px;
  padding: 10px;
  border-radius: 4px;
  border: 1px solid #ccc;
  background-color: #fff;
  &__header {
    display: flex;
    justify-content: space-between;
    position: relative;
    z-index: 1;
    margin-bottom: 20px;
  }
  &__title {
    font-size: 18px;
    font-weight: 400;
  }

  &__close{
    cursor: pointer;
    width: 20px;
    transition: all .3s ease;
    &:hover {
      transform: scale(1.1);
    }
    img {
      width: 100%;
    }
  }
  &__selected-cities{
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  &__city{
    padding: 5px;
    background: antiquewhite;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  &__hamburger{
    pointer-events: none;
    width: 24px;
    height: 24px;
    cursor: move;
    img{
      width: 100%;
    }
  }
  &__delete{
    width: 16px;
    cursor: pointer;
    transition: all .3s ease;
    &:hover {
      transform: scale(1.1);
    }
    img{
      width: 100%;
    }
  }
  &__input{
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-top: 20px;
    input{
      padding: 5px;
    }
    button{
      width: 100%;
      padding: 5px;
      cursor: pointer;
    }
  }
}
</style>