<template>
  <div class="container">

    <div class="carrousell" v-if="showCarousel">
      <!-- Carousel -->
      <b-carousel id="carousel1" controls indicators style="max-height: 400px;">
        <b-carousel-slide v-for="(libro, index) in libros" :key="index" :img-src="libro.imagen" :caption="libro.titulo">
        </b-carousel-slide>
      </b-carousel>
    </div>


    <div class="row justify-content-center">
      <div class="buttons mt-5">
        <b-button variant="primary" class="ms-3 me-3" size="lg" @click="sortByAuthor">Ordenar por autor</b-button>
        <b-button variant="primary" class="ms-3 me-3" size="lg" @click="sortByDate">Ordenar por fecha</b-button>

        <b-button variant="primary" class="ms-3 me-3" size="lg">Mostrar si tiene imagen</b-button>
      </div>
    </div>

    <div class="libros">

      <div class="cards ms-3 mt-5 me-3" v-for="(libro, index) in libros" :key="index" :draggable="true"
        @dragstart="dragStart(index)" @dragover.prevent @drop="drop(index)">
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
          <b-button variant="outline-success" @click="showFormCreate = true">
            <img src="../assets/plus.png" />
          </b-button>
        </div>
        <div class="mt-5" @dragover.prevent @drop="editCard">
          <b-button variant="outline-warning">
            <img src="../assets/pencil.png" />
          </b-button>
        </div>
        <div class="mt-5">
          <b-button variant="outline-danger" @dragover.prevent @drop="deleteCard">
            <img src="../assets/trash.png" />
          </b-button>
        </div>
      </div>
    </div>

    <!-- Formulario crear libro-->
    <b-modal id="modal-center" v-model="showFormCreate" title="Agregar Libro">
      <form @submit.prevent="submitForm">
        <b-form-group id="titulo" label="Título:" label-for="titulo-input">
          <b-form-input id="titulo-input" v-model="titulo" required></b-form-input>
        </b-form-group>

        <b-form-group id="autor" label="Autor:" label-for="autor-input" class="mt-2">
          <b-form-input id="autor-input" v-model="autor" required></b-form-input>
        </b-form-group>

        <b-form-group id="fecha" label="Fecha de Publicación:" label-for="fecha-input" class="mt-2">
          <b-form-input id="fecha-input" v-model="fecha" type="date" required></b-form-input>
        </b-form-group>

        <b-button class="mt-3 me-3 btn-create" type="submit" variant="primary" @click="">Agregar libro</b-button>
      </form>
      <template #modal-footer>

        <b-button class="mt-3" variant="danger" @click="cancelFormCreate">Cancelar</b-button>
      </template>
    </b-modal>


    <!-- Formulario editar libro-->
    <b-modal id="modal-center" v-model="showFormEdit" title="Modificar Libro">
      <form @submit.prevent="submitFormEdit">
        <b-form-group id="titulo" label="Título:" label-for="titulo-input">
          <b-form-input id="titulo-input" v-model="titulo" required></b-form-input>
        </b-form-group>

        <b-form-group id="autor" label="Autor:" label-for="autor-input" class="mt-2">
          <b-form-input id="autor-input" v-model="autor" required></b-form-input>
        </b-form-group>

        <b-form-group id="fecha" label="Fecha de Publicación:" label-for="fecha-input" class="mt-2">
          <b-form-input id="fecha-input" v-model="fecha" type="date" required></b-form-input>
        </b-form-group>

        <b-button class="mt-3 me-3 btn-create" type="submit" variant="primary" @click="">Guardar Cambios</b-button>
      </form>

      <template #modal-footer>

        <b-button class="mt-3" variant="danger" @click="cancelFormEdit">Cancelar</b-button>
      </template>
    </b-modal>


  </div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      showCarousel: true,
      lastScrollPosition: 0,

      showFormCreate: false,
      titulo: '',
      autor: '',
      fecha: '',
      libros: []


    };
  },
  mounted() {
    this.getBooks();
    window.addEventListener('scroll', this.handleScroll);
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.handleScroll); // eliminar el listener cuando el componente se destruya
  },
  methods: {
    handleScroll() {
      const currentScrollPosition = window.scrollY;
      if (currentScrollPosition > this.lastScrollPosition && currentScrollPosition > 1000) {
        this.showCarousel = false; // Oculta el carrusel si el usuario hace scroll hacia abajo más de 100 píxeles
      } else {
        this.showCarousel = true; // Muestra el carrusel en cualquier otro caso
      }
      this.lastScrollPosition = currentScrollPosition;
    },
    sortByAuthor() {
      // Utiliza el método sort para ordenar los libros por autor
      this.libros.sort((a, b) => {
        // Compara los autores en minúsculas para asegurarse de que la comparación sea insensible a mayúsculas
        return a.autor.toLowerCase().localeCompare(b.autor.toLowerCase());
      });
    },
    sortByDate() {
      // Utiliza el método sort para ordenar los libros por fecha
      this.libros.sort((a, b) => {
        // Convierte las fechas a objetos Date para que puedan ser comparadas
        const dateA = new Date(a.fecha);
        const dateB = new Date(b.fecha);
        // Compara las fechas
        return dateA - dateB;
      });
    },
    submitForm() {
      // Aquí puedes enviar los datos a tu backend o hacer lo que necesites con ellos
      console.log("Datos del formulario:", this.titulo, this.autor, this.fecha);
      this.createBook();
      // Limpia los campos del formulario
      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario después de enviar
      this.showFormCreate = false;
    },
    submitFormEdit() {
      // Aquí puedes enviar los datos a tu backend o hacer lo que necesites con ellos
      console.log("Datos del formulario:", this.titulo, this.autor, this.fecha);
      // Limpia los campos del formulario
      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario después de enviar
      this.showFormEdit = false;
    },
    cancelFormCreate() {
      // Limpia los campos del formulario si se cancela
      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario
      this.showFormCreate = false;
    },
    cancelFormEdit() {
      // Limpia los campos del formulario si se cancela
      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario
      this.showFormEdit = false;
    },
    dragStart(index) {
      // Guarda el índice de la tarjeta que se está arrastrando
      this.draggedIndex = index;
    },
    drop(index) {
      // Intercambia las tarjetas
      const draggedLibro = this.libros[this.draggedIndex];
      this.libros.splice(this.draggedIndex, 1);
      this.libros.splice(index, 0, draggedLibro);
    },
    editCard() {
      // Abrir el formulario con los datos de la tarjeta arrastrada hacia el botón de lápiz
      const editedLibro = this.libros[this.draggedIndex];
      this.titulo = editedLibro.titulo;
      this.autor = editedLibro.autor;
      this.fecha = editedLibro.fecha;
      this.showFormEdit = true;
    },
    deleteCard() {
      // Elimina la tarjeta arrastrada hacia el botón de eliminar
      const deletedLibro = this.libros[this.draggedIndex];
      this.libros.splice(this.draggedIndex, 1);
      alert(`Se eliminó correctamente el libro "${deletedLibro.titulo}".`);
    },
    getBooks() {
      axios.get('http://localhost:8080/api/v1/book/getAll')
        .then(response => {
          console.log(response)
          this.libros = response.data.data.map(libro => {
            return {
              titulo: libro.title,
              autor: libro.author,
              fecha: libro.date,
              imagen: libro.image
            }
          })
          console.log(this.libros)
        })
        .catch(error => {
          console.error("Hubo un error al obtener usuarios", error)
        })
    },
    createBook() {
      axios.post('http://localhost:8080/api/v1/book/create', {
        title: this.titulo,
        author: this.autor,
        date: this.fecha
      })
        .then(response => {
          this.getBooks();
          console.log(response)
        })
        .catch(error => {
          console.error("Hubo un error al crear el libro", error)
        })
    }
  }
};


</script>

<style scoped>
.libros {
  display: flex;
  flex-wrap: wrap;
  /* Ajusta el margen para dar espacio entre las tarjetas */
  margin: -10px;
}

.cards {
  flex: 1 0 25%;
  /* Establece el ancho máximo de cada tarjeta al 25% del contenedor padre */
  box-sizing: border-box;
  /* Incluye padding y border en el ancho */
  margin: 10px;
  /* Espacio entre las tarjetas */
}

.crud {
  position: fixed;
  top: 50%;
  /* Ajusta la posición vertical según tus preferencias */
  right: 20px;
  /* Ajusta la posición horizontal según tus preferencias */
  transform: translateY(-50%);
  /* Centra verticalmente */
  width: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.btn-create {
  width: 100%;
  margin-top: 10px;
}

.carrousell {
  margin-top: 20px;
  margin-bottom: 20%;
}
</style>