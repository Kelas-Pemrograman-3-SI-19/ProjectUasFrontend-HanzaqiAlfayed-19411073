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
              <div class="text-h6">Data Transaksi</div>
              <div>Data Transaksi Pembelian Dan Pemesanan</div>
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
                  {{ props.row.dataRental[0].namapenyewa }}
                </q-td>
                 <q-td key="hargasewaperjam" :props="props">
                  Rp. {{ props.row.dataRental[0].hargasewaperjam }},-
                </q-td>
                 <q-td key="alamatpenyewa" :props="props">
                  {{ props.row.dataRental[0].alamatpenyewa }}
                </q-td>
                 <q-td key="jenismobil" :props="props">
                   {{ props.row.dataRental[0].jenismobil }}
                </q-td>
                 <q-td key="nama" :props="props">
                  {{ props.row.dataUser[0].namaLengkap }}
                </q-td>
                  <q-td key="status" :props="props">
                  <q-badge v-if="props.row.status === 1" color="red" class="q-pa-sm">
                    Belum Dikonfirmasi
                  </q-badge>
                  <q-badge v-else-if="props.row.status === 2" color="orange" class="q-pa-sm">
                    Sedang Dalam Pengiriman
                  </q-badge>
                  <q-badge v-else color="green" class="q-pa-sm">
                    Sudah Diterima Pembeli
                  </q-badge>
                </q-td>
                  <q-td key="aksi" :props="props">
                    <q-btn @click="openDetail(props.row)" label="Detail" color="black" unelevated />
                    <q-btn :disabled="props.row.status !== 1" @click="confirm(props.row._id)" label="Konfirmasi" class="q-ml-lg" color="blue" unelevated />
                </q-td>
              </q-tr>
            </template>
            </q-table>
      </q-card-section>
    </q-card>
    <q-dialog
      v-model="detail"
      v-if="detail"
    >
      <q-card style="width: 700px; max-width: 80vw;">
        <q-card-section>
          <div class="text-h6">Detail Order</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <div class="row">
            <div class="col">
              <div class="text-caption">
                Nama Penyewa
              </div>
              <div class="text-weight-bold">
                {{activeData.dataUser[0].namaLengkap}}
              </div>
            </div>
            <div class="col">
              <div class="text-caption">
                Rental Mobil
              </div>
              <div class="text-weight-bold">
                {{activeData.dataRental[0].namapenyewa}}
              </div>
            </div>
            <div class="col">
              <div class="text-caption">
                Harga
              </div>
              <div class="text-weight-bold">
                Rp. {{activeData.hargasewaperjam}},-
              </div>
            </div>
            <div class="col">
              <div class="text-caption">
              </div>
              <div class="text-weight-bold">
                {{activeData.total}}
              </div>
            </div>
          </div>
          <div class="row">
            <q-img :src="`http://localhost:5000/${activeData.image}`"></q-img>
          </div>
        </q-card-section>

        <q-card-actions align="right" class="bg-white text-teal">
          <q-btn flat label="OK" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
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
        { name: 'nama', align: 'nama', label: 'nama', field: 'nama', sortable: true },
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
      this.$axios.get('order/getallorder')
        .then((res) => {
          if (res.data.sukses) {
            this.data = res.data.data
          }
        })
    },
    openDetail (data) {
      this.activeData = data
      this.detail = true
    },
    confirm (id) {
      this.$q.dialog({
        title: 'Konfirmasi',
        message: 'Apakah Anda Yakin Menerima Order tersebut?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        this.$axios.put(`order/konfirmasiorder/${id}`)
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
