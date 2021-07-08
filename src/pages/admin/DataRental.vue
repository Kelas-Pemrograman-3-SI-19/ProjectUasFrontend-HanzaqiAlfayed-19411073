<template>
  <q-page padding>
    <div class="row q-mb-md col-gutter-md">
      <div class="col-md-12 col-xs-12 col-lg-12">
        <div class="row">
          <div class="col-auto">
            <div class="left blue"></div>
          </div>
          <div class="col">
            <q-banner inline-actions class="text-b;ue-grey-12">
              <div class="text-h6">Data Rental</div>
              <div>Data Katalog Rental Anda</div>
            </q-banner>
          </div>
        </div>
      </div>
    </div>
    <q-card flat>
      <q-card-section>
            <q-table
            :data="data"
            :columns="columns"
            row-key="name"
          >
            <template v-slot:body="props">
              <q-tr :props="props">
                <q-td key="namapenyewa" :props="props">
                  <div>
                  {{ props.row.namapenyewa}}
                  </div>
                </q-td>
                <q-td key="alamatpenyewa" :props="props">
                   {{ props.row.alamatpenyewa }}
                </q-td>
                <q-td key="hargasewaperjam" :props="props">
                 Rp. {{ props.row.hargasewaperjam }},-
                </q-td>
                <q-td key="jenismobil" :props="props">
                  {{ props.row.jenismobil }}
                </q-td>
                <q-td key="gambar" :props="props">
                <q-img
                  :src="`http://localhost:5000/${props.row.image}`"
                  spinner-color="blue"
                  style="height: 120px; max-width: 85px"
                  />
                </q-td>
                <q-td key="aksi" :props="props">
                  <div class="row q-gutter-md">
                    <q-btn :to="{ name: 'formEdit', params: { id: props.row._id }}" label="Edit" icon="edit" color="orange"/>
                    <q-btn @click="deleteRental(props.row._id)" label="Hapus" icon="delete" color="red"/>
                  </div>
                </q-td>
              </q-tr>
            </template>
            </q-table>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      columns: [
        { name: 'namapenyewa', align: 'left', label: 'Nama Penyewa', field: 'namapenyewa', sortable: true },
        { name: 'alamatpenyewa', align: 'left', label: 'Alamat Penyewa', field: 'alamatpenyewa', sortable: true },
        { name: 'hargasewaperjam', align: 'left', label: 'Harga Sewaperjam', field: 'hargasewaperjam', sortable: true },
        { name: 'jenismobil', align: 'left', label: 'jenismobil', field: 'jenismobil', sortable: true },
        { name: 'gambar', align: 'left', label: 'Gambar', field: 'gambar' },
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
      this.$axios.get('rental/getall')
        .then((res) => {
          if (res.data.sukses) {
            this.data = res.data.data
          } else {
            this.showNotif(res.data.pesan, 'negative')
          }
        })
    },
    deleteRental (id) {
      this.$q.dialog({
        title: 'Konfirmasi',
        message: 'Apakah Anda Yakin Menghapus ini?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        this.$axios.delete(`rental/delete/${id}`)
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
.left {
  width: 5px;
  height: 100%;
  background-color: aqua;
}
</style>