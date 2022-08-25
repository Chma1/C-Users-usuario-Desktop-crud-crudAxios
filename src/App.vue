<template>
  <v-app>
    <v-main>
      <v-container>
        <v-col cols="12" class="mt-4 mb-4">
          <v-form id="formulario">
            <v-row>
              <v-col cols="2">
                <v-text-field
                  label="Producto"
                  v-model="producto.name"
                ></v-text-field>
              </v-col>
              <v-col cols="2">
                <v-text-field
                  label="Precio"
                  placeholder="$"
                  v-model="producto.price"
                ></v-text-field>
              </v-col>
              <v-col cols="2">
                <v-text-field label="Iva" v-model="producto.iva"></v-text-field>
              </v-col>
              <v-col cols="2">
                <v-text-field
                  label="Stock"
                  v-model="producto.stock"
                ></v-text-field>
              </v-col>
              <v-col cols="2">
                <v-btn
                  class="mt-2 ma-2"
                  color="primary"
                  @click="agregarProducto()"
                  >Registrar
                </v-btn>
              </v-col>
              <v-col cols="2">
                <v-btn class="mt-2" color="success" @click="modificarProducto()"
                  >Actualizar</v-btn
                >
              </v-col>
            </v-row>
          </v-form>

          <v-table class="mt-4">
            <thead>
              <tr>
                <th class="text-left">id</th>
                <th class="text-left">Producto</th>
                <th class="text-left">Precio</th>
                <th class="text-left">Iva</th>
                <th class="text-left">Stock</th>
                <th class="text-center">Aciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(producto, index) in productos" :key="index">
                <td>{{ producto._id }}</td>
                <td>{{ producto.Name }}</td>
                <td>$ {{ producto.Price }}</td>
                <td>{{ producto.Stock }}</td>
                <td>1</td>
                <td class="d-flex justify-space-around">
                  <v-btn
                    color="secondary"
                    elevation="2"
                    variant="outlined"
                    class="mt-1"
                    @click="editarProducto(index)"
                    >Editar</v-btn
                  >
                  <v-btn
                    class="mt-1"
                    color="warning"
                    @click="eliminarProducto(producto._id)"
                    >Eliminar</v-btn
                  >
                </td>
              </tr>
            </tbody>
          </v-table>
        </v-col>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data: () => ({
    productos: [],

    producto: {
      name: "",
      price: "",
      stock: "",
      iva: 0,
    },
  }),

  async mounted() {
    await this.getProducto();
  },
  methods: {
    async getProducto() {
      const responseAPI = (
        await axios({
          url: "https://clicko.com.mx/crud/v1/product",
          method: "GET",
        })
      ).data.api;
      // console.log(responseAPI.data);
      this.productos = responseAPI.data;
    },
    async agregarProducto() {
      const productoNuevo = (
        await axios({
          url: "https://clicko.com.mx/crud/v1/product",
          method: "POST",
          data: this.producto,
        })
      ).data.api;
      // if (name == "") {
      //   return console.log("Cmpos vacios");
      // }
      this.productos.unshift(productoNuevo.data);

      document.getElementById("formulario").reset();
      console.log(productoNuevo);
    },
    editarProducto(index) {
      const product_temp = this.productos[index];
      // console.log(product_temp);
      this.producto = {
        name: product_temp.Name,
        price: product_temp.Price,
        stock: product_temp.Stock,
        iva: product_temp.iva,
        _id: product_temp._id,
      };
    },

    async modificarProducto() {
      const actualizarProducto = (
        await axios({
          url: "https://clicko.com.mx/crud/v1/product/",
          method: "PUT",
          data: this.producto,
        })
      ).data.api;
      console.log(actualizarProducto);
      // actualizarProducto.data;
      this.productos.filter((value, index) => {
        if (value._id === this.producto._id) {
          this.productos[index] = actualizarProducto.data;
        }
      });
      document.getElementById("formulario").reset();
    },

    async eliminarProducto(id) {
      const eliminarArticulo = (
        await axios({
          url: "https://clicko.com.mx/crud/v1/product/" + id,
          method: "DELETE",
          // data: id,
        })
      ).data.api;
      console.log(eliminarArticulo);
      this.productos = this.productos.filter((value) => value._id != id);
    },
  },
};
</script>
