<template>
    <q-page padding>
        <div class="row q-mb-md col-gutter-md">
          <div class="col-md-12 col-xs-12 col-lg-12">
            <div class="row">
              <div class="col-auto">
                <div class="left blue"></div>
              </div>
              <div class="col">
                <q-banner inline-actions class="text-blue-grey-14">
                  <div class="text-h6">Edit Mapala</div>
                  <div>Edit Data Mapala</div>
                </q-banner>
              </div>
            </div>
          </div>
        </div>
        <q-card flat>
          <q-card-section class="row">
            <q-form
              @submit="onSubmit()"
              class="q-gutter-md col-md-6 col-xs-12"
            >
              <q-input
                v-model="form.namalengkap"
                filled
                label="namalengkap"
                :rules="[ val => val && val.length > 0 || 'Mohon Isi namalengkap']"
              />

              <q-input
                v-model="form.hargapendaftaran"
                filled
                label="hargapendaftaran"
                :rules="[ val => val > 0 || 'Mohon Isi hargapendaftaran']"
              />

              <q-input
                v-model="form.fakultas"
                filled
                label="fakultas"
                :rules="[ val => val && val.length > 0 || 'Mohon Isi fakultas']"
              />

              <q-select
                filled
                v-model="form.divisi"
                :options="optiondivisi"
                label="Pilih Genre"
              />

              <div class="flex">
                Pilih Rating Mapala
                <q-rating
                  v-model="form.rating"
                  size="2em"
                  :max="5"
                  class="q-ml-md"
                  color="primary"
                />
              </div>

              <q-input
                v-model="form.deskripsi"
                filled
                label="Deskripsi Mapala"
                type="textarea"
              />

              <q-file accept=".jpg, image/*" color="teal" filled v-model="image" label="Upload gambar">
                <template v-slot:prepend>
                  <q-icon name="cloud_upload" />
                </template>
              </q-file>

              <div>
                <q-btn label="Submit" type="submit" color="primary"/>
                <q-btn label="Reset" type="reset" color="primary" flat class="q-ml-sm" />
              </div>
            </q-form>
          </q-card-section>
        </q-card>
    </q-page>
</template>
<script>
export default {
  data () {
    return {
      form: {
        namalengkap: null,
        hargapendaftaran: 0,
        fakultas: null,
        divisi: null,
        rating: 0,
        deskripsi: null
      },
      optiondivisi: [
        'hutan gunung',
        'rock climbing'
      ],
      image: null
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$axios.get(`mapala/getbyid/${this.$route.params.id}`)
        .then(res => {
          if (res.data.sukses) {
            this.form = res.data.data
          } else {
            this.$router.push({ name: 'dataMapala' })
          }
        })
    },
    onSubmit () {
      const formData = new FormData()
      formData.append('image', this.image)
      formData.append('data', JSON.stringify(this.form))
      this.$axios.put(`mapala/edit/${this.$route.params.id}`, formData)
        .then(res => {
          if (res.data.sukses) {
            this.$showNotif(res.data.pesan, 'positive')
            this.$router.push({ name: 'dataMapala' })
          } else {
            this.$showNotif(res.data.pesan, 'negative')
          }
        })
    }
  }
}
</script>
<style scoped>
.left{
  width: 5px;
  height: 100%;
  background-color:rgb(89, 251, 251);
}
</style>
