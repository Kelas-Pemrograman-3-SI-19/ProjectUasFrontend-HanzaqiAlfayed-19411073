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
              <div class="text-h6">My Transaksi</div>
              <div>Data Transaksi Pembelian Dan Pemesanan Anda</div>
            </q-banner>
          </div>
        </div>
      </div>
    </div>
    <q-card flat>
        <q-table
            :data="data"
            :columns="columns"
            row-key="name"
          >
            <template v-slot:body="props">
              <q-tr :props="props">
                <q-td key="namapenyewa" :props="props">
                  {{ props.row.dataRental[0].namapenyewa }}
                </q-td>
                <q-td key="hargasewaperjam" :props="props">
                  Rp. {{ props.row.hargasewaperjam }},-
                </q-td>
                 <q-td key="alamatpenyewa" :props="props">
                  {{ props.row.alamatpenyewa }}
                </q-td>
                 <q-td key="jenismobil" :props="props">
                   {{ props.row.jenismobil }}
                </q-td>
                  <q-td key="status" :props="props">
                  <q-badge v-if="props.row.status === 1" color="red" class="q-pa-sm">
                    Belum Dikonfirmasi
                  </q-badge>
                  <q-badge v-else-if="props.row.status === 2" color="orange" class="q-pa-sm">
                    Sedang Dalam Pengiriman
                  </q-badge>
                  <q-badge v-else color="green" class="q-pa-sm">
                    Sudah Diterima
                  </q-badge>
                </q-td>
                  <q-td key="aksi" :props="props">
                    <q-btn :disabled="props.row.status !== 2" @click="confirm(props.row._id)" label="Terima Barang" class="q-ml-lg" color="blue" unelevated />
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
        { name: 'namapenyewa', align: 'center', label: 'namapenyewa', field: 'namapenyewa', sortable: true },
        { name: 'hargasewaperjam', align: 'center', label: 'hargasewaperjam', field: 'hargasewaperjam', sortable: true },
        { name: 'alamatpenyewa', align: 'center', label: 'alamatpenyewa', field: 'alamatpenyewa', sortable: true },
        { name: 'jenismobil', align: 'center', label: 'jenismobil', field: 'jenismobil', sortable: true },
        { name: 'status', align: 'center', label: 'Status', field: 'status', sortable: true },
        { name: 'aksi', align: 'center', label: 'Aksi', field: 'aksi' }
      ],
      data: [],
      detail: false,
      activeData: null
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$axios.get(`order/getorderbyuser/${this.$q.localStorage.getItem('dataUser')._id}`)
        .then((res) => {
          if (res.data.sukses) {
            this.data = res.data.data
          }
        })
    },
    confirm (id) {
      this.$q.dialog({
        title: 'Konfirmasi',
        message: 'Apakah Anda Yakin Sudah Menerima ?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        this.$axios.put(`order/terimabarang/${id}`)
          .then((res) => {
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
