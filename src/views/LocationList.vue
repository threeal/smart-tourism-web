<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="10">
        <v-data-table
          :headers="headers"
          :items="locations"
          sort-by="id"
          class="elevation-1"
        >
          <template v-slot:top>
            <v-toolbar flat>
              <v-toolbar-title>Daftar Lokasi</v-toolbar-title>
              <v-divider class="mx-4" inset vertical />
              <v-spacer />
              <LocationDialog />
            </v-toolbar>
          </template>
          <template v-slot:[`item.type`]="{ item }">
            {{ typeText(item) }}
          </template>
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon @click="edit(item)" small class="mr-2">
              mdi-pencil
            </v-icon>
            <v-icon @click="remove(item)" small class="mr-2">
              mdi-delete
            </v-icon>
          </template>
          <template v-slot:no-data> Tidak ada data </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import LocationDialog from "../components/LocationDialog";
import { mapState } from "vuex";

export default {
  name: "LocationList",
  components: {
    LocationDialog
  },
  data: () => ({
    headers: [
      { text: "ID", value: "id" },
      { text: "Nama", value: "name" },
      { text: "Jenis", value: "type" },
      { text: "Sudut Bujur", value: "longitude" },
      { text: "Sudut Lintang", value: "latitude" },
      { text: "Deskripsi", value: "description" },
      { text: "Perintah", value: "actions", sortable: false }
    ]
  }),
  computed: {
    ...mapState("location", ["locations"])
  },
  methods: {
    typeText(item) {
      switch (item.type) {
        case "information":
          return "Informasi";
        case "gallery":
          return "Galeri";
        case "garden":
          return "Taman";
        case "rides":
          return "Wahana";
        case "parking_area":
          return "Tempat Parkir";
        case "Restroom":
          return "Toilet";
        case "gift_shop":
          return "Toko Suvernir";
        case "food_court":
          return "Tempat Makan";
        default:
          return "-";
      }
    },
    edit(location) {
      this.$store.dispatch("location/select", { location: location });
    },
    remove(location) {
      this.$store.dispatch("confirmation/ask", {
        message: `Apakah anda yakin ingin menghapus lokasi "${location.name}"?`,
        callback: () => {
          return this.$store.dispatch("location/remove", {
            info: true,
            locationId: location.id
          });
        }
      });
    }
  },
  mounted() {
    this.$store.dispatch("location/findAll", { info: true });
  }
};
</script>
