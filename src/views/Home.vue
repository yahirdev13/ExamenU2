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

        <b-button variant="primary" class="ms-3 me-3" size="lg" @click="showImageInfoModal">Mostrar si tiene
          imagen</b-button>

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
      id: '',
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
    window.removeEventListener('scroll', this.handleScroll);
  },
  methods: {
    handleScroll() {
      const currentScrollPosition = window.scrollY;
      if (currentScrollPosition > 200 && currentScrollPosition > this.lastScrollPosition) {

        this.showCarousel = false;
      } else if (currentScrollPosition < this.lastScrollPosition) {

      } else {

        this.showCarousel = true;
      }
      this.lastScrollPosition = currentScrollPosition;
    },
    sortByAuthor() {

      this.libros.sort((a, b) => {

        return a.autor.toLowerCase().localeCompare(b.autor.toLowerCase());
      });
    },
    sortByDate() {

      this.libros.sort((a, b) => {

        const dateA = new Date(a.fecha);
        const dateB = new Date(b.fecha);

        return dateA - dateB;
      });
    },
    showImageInfoModal() {

      this.$bvModal.msgBoxOk('Todos los libros se crean con una imagen aleatoria.');
    },
    submitForm() {


      this.createBook();

      this.titulo = '';
      this.autor = '';
      this.fecha = '';

      this.showFormCreate = false;
    },
    submitFormEdit() {

      console.log("Datos del formulario:", this.titulo, this.autor, this.fecha);

      this.updateBook(this.id);
      this.titulo = '';
      this.autor = '';
      this.fecha = '';

      this.showFormEdit = false;
    },
    cancelFormCreate() {

      this.titulo = '';
      this.autor = '';
      this.fecha = '';

      this.showFormCreate = false;
    },
    cancelFormEdit() {

      this.titulo = '';
      this.autor = '';
      this.fecha = '';
      // Oculta el formulario
      this.showFormEdit = false;
    },
    dragStart(index) {

      this.draggedIndex = index;
    },
    drop(index) {

      const draggedLibro = this.libros[this.draggedIndex];
      this.libros.splice(this.draggedIndex, 1);
      this.libros.splice(index, 0, draggedLibro);
    },
    editCard() {

      const editedLibro = this.libros[this.draggedIndex];
      this.id = editedLibro.id;
      this.titulo = editedLibro.titulo;
      this.autor = editedLibro.autor;
      this.fecha = editedLibro.fecha;
      this.showFormEdit = true;
    },
    deleteCard() {

      const deletedLibro = this.libros[this.draggedIndex];
      this.id = deletedLibro.id;
      this.deleteBook(this.id);
      alert(`Se eliminó correctamente el libro "${deletedLibro.titulo}".`);
    },
    getBooks() {
      axios.get('http://localhost:8080/api/v1/book/getAll')
        .then(response => {
          console.log(response)
          this.libros = response.data.data.map(libro => {
            return {
              id: libro.id,
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
    },
    updateBook(id) {
      axios.put(`http://localhost:8080/api/v1/book/update/${id}`, {
        title: this.titulo,
        author: this.autor,
        date: this.fecha
      })
        .then(response => {
          console.log(response)
          this.getBooks();
        })
        .catch(error => {
          console.error("Hubo un error al actualizar el libro", error)
        })
    },
    deleteBook(id) {
      axios.delete(`http://localhost:8080/api/v1/book/delete/${id}`)
        .then(response => {
          console.log(response)
          this.getBooks();
        })
        .catch(error => {
          console.error("Hubo un error al eliminar el libro", error)
        })
    },



  }
};


</script>

<style scoped>
.libros {
  display: flex;
  flex-wrap: wrap;

  margin: -10px;
}

.cards {
  flex: 1 0 25%;

  box-sizing: border-box;

  margin: 10px;

}

.crud {
  position: fixed;
  top: 50%;

  right: 20px;

  transform: translateY(-50%);

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