<template>
  <div class="container">


    <div class="buttons mt-5">
      <b-button variant="primary" class="ms-3 me-3" size="lg">Ordenar por autor</b-button>
      <b-button variant="primary" class="ms-3 me-3" size="lg">Ordenar por fecha</b-button>
      <b-button variant="primary" class="ms-3 me-3" size="lg">mostrar si tiene imagen</b-button>
    </div>

    <div class="cards ms-3 mt-5 me-3">
      <b-card img-src="https://picsum.photos/600/300/?image=25" img-alt="Image" img-top tag="article"
        style="max-width: 20rem;" class="mb-2">
        <b-card-text>
          Aqui va descripción random del libro
        </b-card-text>
      </b-card>
    </div>

    <div class="crud">

      <div class="mt-5">
        <b-button variant="outline-success">
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
</template>

<script>
export default {

}
</script>

<style scoped>
.container {
  display: flex;
  justify-content: flex-end;
  margin-left: auto;
  /* Esto empujará el contenedor hacia la derecha */
  width: calc(100% - 200px);
  /* Establece el ancho menos el ancho del contenedor "crud" */
}

.crud {
  width: 200px;
}
</style>