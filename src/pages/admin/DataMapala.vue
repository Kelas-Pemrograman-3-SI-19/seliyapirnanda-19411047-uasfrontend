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
              <div class="text-h6">Data mapala</div>
              <div>Data katalog mapala</div>
            </q-banner>
          </div>
        </div>
      </div>
    </div>
    <q-card flat>
      <q-table
        :data="data"
        flat
        :columns="columns"
        row-key="name"
      >
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td key="divisi" :props="props">
              {{ props.row.divisi }}
            </q-td>
            <q-td key="deskripsi" :props="props">
              <div class="ellipsis" style="max-width:100px;">
                {{ props.row.deskripsi }}
              </div>
            </q-td>
            <q-td key="gambar" :props="props">
              <q-img
                :src="`http://localhost:5000/${props.row.image}`"
              spinner-color="white"
              />
            </q-td>
            <q-td key="aksi" :props="props">
              <div class="row q-gutter-md">
                <q-btn :to="{ name:'editMapala', params: { id: props.row._id }}" label="Edit" icon="edit" color="warning" unelevated/>
                <q-btn @click="deleteMapala(props.row._id)" label="Hapus" icon="delete" color="negative" unelevated/>
              </div>
            </q-td>
          </q-tr>
        </template>
      </q-table>
    </q-card>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      columns: [
        { name: 'divisi', align: 'left', label: 'divisi', field: 'divisi', sortable: true },
        { name: 'deskripsi', align: 'left', label: 'Deskripsi ', field: 'deskripsi', sortable: true },
        { name: 'gambar', align: 'left', label: 'gambar', field: 'gambar', sortable: true },
        { name: 'aksi', align: 'left', label: 'Aksi', field: 'aksi' }
      ],
      data: []
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$axios.get('/mapala/getall')
        .then((res) => {
          if (res.data.sukses) {
            this.data = res.data.data
          } else {
            this.showNotif(res.data.pesan, 'negative')
          }
        })
    },
    deleteMapala (id) {
      this.$q.dialog({
        title: 'Konfirmasi',
        message: 'Apakah Anda Yakin',
        cancel: true,
        persistent: true
      }).onOk(() => {
        this.$axios.delete(`mapala/delete/${id}`)
          .then(res => {
            if (res.data.sukses) {
              this.$showNotif(res.data.pesan, 'positive')
              this.getData()
            } else {
              this.$showNotif(res.data.pesan, 'negative')
            }
          })
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
