<template>
    <q-page padding>
        <div class="q-mb-xl">
          <q-carousel
            animated
            v-model="slide"
            navigation
            infinite
            :autoplay="autoplay"
            arrows
            swipeable
            transition-prev="jump-right"
            transition-next="jump-left"
            @mouseenter="autoplay = false"
            @mouseleave="autoplay = true"
          >
            <q-carousel-slide :name="1" img-src="https://pbs.twimg.com/profile_images/3750063881/86cabf5eda3735eef47b32e9ba9de1bd.jpeg">
            <div class="absolute-center custom-caption" style="text-align:center; color:black;">
              <div class="text-h2 text-weight-bold">Welcome to UKM</div>
              <div class="text-overline">Enjoy the UKM</div>
            </div>
            </q-carousel-slide>
            <q-carousel-slide :name="2" img-src="https://ubl.ac.id/wp-content/uploads/2015/11/6-e1513649090191.jpg" />
            <q-carousel-slide :name="3" img-src="https://cdn0-production-images-kly.akamaized.net/dLdQhTAZb9iwV8gOCrn8CZlIed0=/640x360/smart/filters:quality(75):strip_icc():format(jpeg)/kly-media-production/medias/1488007/original/043611600_1485430753-Banner_Mapala.jpg" />
            <q-carousel-slide :name="4" img-src="https://pcr.ac.id/assets/media/pcr_media20170227092920.PNG" />
            <q-carousel-slide name="soft-jazz">
              <q-video
                class="absolute-full"
                src="https://i1.wp.com/ahlipembuatlapangan.com/wp-content/uploads/2016/09/bisnis-lapangan-futsal-kontraktor-lapangan-futsal-ahli-pembuat-lapangan-futsal-pasang-rumput-sintetis-pasang-matras-interlock.jpg"
              />
            </q-carousel-slide>
          </q-carousel>
        </div>
        <div class="row q-col-gutter-md">
          <div class="col-md-3 col-xs-12" v-for="(mapala, i) in data" :key="i">
            <q-card>
              <q-img :src="`${$baseImageURL}/${mapala.image}`" :ratio="16/9" />

              <q-card-section>
                <q-btn
                  fab
                  color="warning"
                  icon="add_shopping_cart"
                  class="absolute"
                  style="top: 0; right: 12px; transform: translateY(-50%);"
                />

                <q-rating v-model="mapala.rating" readonly color="orange-5" :max="5" size="32px" />
              </q-card-section>

              <q-card-section class="q-pt-none">
                <div class="text-subtitle1">
                  Rp {{ mapala.hargapendaftaran }},-
                </div>
                <div class="text-subtitle1">
                  {{ mapala.bataspendaftaran }}
                </div>
                  <div class="text-subtitle1">
                  {{ mapala.divisi }}
                </div>
                <div @click="mapala.expanded = !mapala.expanded" class="text-caption text-grey-10 cursor-pointer">
                  Tampilkan diskipsi
                </div>
                <q-slide-transition>
                  <div v-show="mapala.expanded">
                    <div class="text-grey text-caption" style="text-align:justify;">
                      {{ mapala.deskripsi }}
                    </div>
                  </div>
                </q-slide-transition>
              </q-card-section>

              <q-card-actions>
                <q-btn unelevated @click="openDetail(mapala)" class="full-width" color="primary">
                  Order Now
                </q-btn>
              </q-card-actions>
            </q-card>
          </div>
        </div>
        <q-dialog v-model="dialog" v-if="dialog" position="bottom">
          <q-card style="width: 500px">
            <q-card-section>
              <div class="text-h6">Detail Pemesanan</div>
            </q-card-section>
              <q-form>
                <q-input filled type="number" class="q-mb-md" v-model="jumlah" label="Masukkan Jumlah"/>
                Rp{{ total }},-
                <q-file color="teal" class="q-mt-md" v-model="image" filled label="Upload Bukti Pembayaran">
                  <template v-slot:prepend>
                    <q-icon name="cloud_upload" />
                  </template>
                </q-file>
              </q-form>
            <q-card-actions align="right">
              <q-btn flat label="Batal" v-close-popup/>
              <q-btn color="primary" @click="prosesTransaksi" label="Proses"/>
            </q-card-actions>
          </q-card>
        </q-dialog>
    </q-page>
</template>
<script>
export default {
  data () {
    return {
      slide: 1,
      autoplay: true,
      stars: 4,
      dialog: false,
      data: [],
      image: null,
      activeData: null,
      jumlah: 1
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$axios.get('mapala/getall')
        .then(res => {
          if (res.data.sukses) {
            this.data = res.data.data.map(mapala => {
              return Object.assign(mapala, {
                expanded: false
              })
            })
          } else {
            this.$showNotif(res.data.pesan, 'negative')
          }
        })
    },
    openDetail (data) {
      this.activeData = data
      this.dialog = true
    },
    prosesTransaksi () {
      const formData = new FormData()
      formData.append('image', this.image)
      formData.append('data', JSON.stringify({
        idUser: this.$q.localStorage.getItem('dataUser')._id,
        idMapala: this.activeData._id,
        hargasewa: this.activeData.hargasewa,
        jumlah: this.jumlah,
        total: this.total
      }))
      this.$axios.post('order/insert', formData)
        .then(res => {
          if (res.data.sukses) {
            this.$showNotif(res.data.pesan, 'positive')
            this.dialog = false
            this.image = null
          } else {
            this.$showNotif(res.data.pesan, 'negative')
          }
        })
    }
  },
  computed: {
    total () {
      return this.activeData.hargasewa * this.jumlah
    }
  }
}
</script>
