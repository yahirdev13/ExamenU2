<template>
  <div class="container">


    <div class="buttons mt-5">
      <b-button variant="primary" class="ms-3 me-3" size="lg">Ordenar por autor</b-button>
      <b-button variant="primary" class="ms-3 me-3" size="lg">Ordenar por fecha</b-button>
      <b-button variant="primary" class="ms-3 me-3" size="lg">mostrar si tiene imagen</b-button>
    </div>

    <div class="libros">

      <div class="cards ms-3 mt-5 me-3" v-for="(libro, index) in libros" :key="index">
        <b-card :img-src="libro.imagen" img-alt="Image" img-top tag="article" style="max-width: 20rem;" class="mb-2">
          <b-card-text>
            <b>Autor:</b> {{ libro.autor }}
          </b-card-text>
          <b-card-text>
            <b>Titulo:</b> {{ libro.titulo }}
          </b-card-text>
          <b-card-text>
            <b>Fecha de publicacion:</b> {{ libro.fecha }}
          </b-card-text>
        </b-card>
      </div>

      <div class="crud">

        <div class="mt-5">
          <b-button variant="outline-success" @click="showForm = true">
            <img src="../assets/plus.png" />
          </b-button>
        </div>
        <div class="mt-5">
          <b-button variant="outline-warning">
            <img src="../assets/pencil.png" />
          </b-button>
        </div>
        <div class="mt-5">
          <b-button variant="outline-danger">
            <img src="../assets/trash.png" />
          </b-button>
        </div>
      </div>
    </div>

    <!-- Formulario -->
    <b-modal v-model="showForm" title="Agregar Libro">
      <form @submit.prevent="submitForm">
        <b-form-group id="titulo" label="Título:" label-for="titulo-input">
          <b-form-input id="titulo-input" v-model="titulo" required></b-form-input>
        </b-form-group>

        <b-form-group id="autor" label="Autor:" label-for="autor-input">
          <b-form-input id="autor-input" v-model="autor" required></b-form-input>
        </b-form-group>

        <b-form-group id="fecha" label="Fecha de Publicación:" label-for="fecha-input">
          <b-form-input id="fecha-input" type="date" v-model="fecha" required></b-form-input>
        </b-form-group>

        <b-button type="submit" variant="primary">Agregar</b-button>
        <b-button variant="danger" @click="cancelForm">Cancelar</b-button>
      </form>
    </b-modal>


  </div>
</template>

<script>
export default {
  data() {
    return {
      showForm: false,
      titulo: '',
      autor: '',
      fecha: '',
      libros: [
        { titulo: 'Libro 1', autor: 'Autor 1', fecha: '2023-01-01', imagen: 'https://picsum.photos/600/300/?image=25' },
        { titulo: 'Libro 2', autor: 'Autor 2', fecha: '2022-05-15', imagen: 'https://picsum.photos/600/300/?image=26' },
        { titulo: 'Libro 3', autor: 'Autor 3', fecha: '2021-11-30', imagen: 'https://picsum.photos/600/300/?image=27' }
      ]
    };
  },
  methods: {
    submitForm() {
      // Aquí puedes enviar los datos a tu backend o hacer lo que necesites con ellos
      console.log("Datos del formulario:", this.titulo, this.autor, this.fecha);
      // Limpia los campos del formulario
      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario después de enviar
      this.showForm = false;
    },
    cancelForm() {
      // Limpia los campos del formulario si se cancela
      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario
      this.showForm = false;
    }
  }
};
</script>

<style scoped>
.libros {
  display: flex;
  /* División de la clase libros en dos columnas */
  flex-wrap: wrap;
}

.cards {
  flex: 1;
  /* La primera columna (cards) toma todo el espacio disponible */
}

.crud {
  width: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
</style>