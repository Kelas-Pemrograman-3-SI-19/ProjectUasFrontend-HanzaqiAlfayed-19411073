<template>
    <q-page padding>
        <div class="q-mb-xl">
            <q-carousel
                animated
                v-model="slide"
                navigation
                infinite
                autoplay
                arrows
                swipeable
                transition-prev="slide-right"
                transition-next="slide-left"
                >
                <q-carousel-slide :name="1" img-src="~assets/3.jpg" />
                <q-carousel-slide :name="2" img-src="~assets/4.jpg" />
                <q-carousel-slide :name="3" img-src="~assets/5.jpg" />
                <q-carousel-slide :name="4" img-src="~assets/6.jpg" />
            </q-carousel>
        </div>
            <q-banner class="bg-dark">
              <div class=" text-orange text-h5">Daftar-daftar list Rental Terbaru Gaes !!!</div>
            </q-banner>
        <div class="row q-col-gutter-md bg-dark">
            <div class="col-md-3 col-xs-12" v-for="(rental, i) in data" :key="i">
                <q-card>
                    <q-img
                    :src="`http://localhost:5000/${rental.image}`"
                    :ratio="16/9"
                    />

                        <q-card-section>
                            <q-btn
                            fab
                            color="green-5"
                            icon="add_shopping_cart"
                            class="absolute"
                            unelevated
                            style="top: 0; right: 12px; transform: translateY(-50%);"
                            />

                            <div class="row no-wrap items-center">
                              <div class="col text-h6 ellipsis">
                                {{ rental.namapenyewa }}
                              </div>
                            </div>

                           
                        </q-card-section>

                        <q-card-section class="q-pt-none">
                            <div class="text-subtitle1">
                            jenis: {{rental.jenismobil}}
                            </div>
                            <q-slide-transition>
                            <div v-show="rental.expanded">
                            </div>
                          </q-slide-transition>
                        </q-card-section>

                        <q-card-actions>
                            <q-btn unelevated @click="openDetail(rental) " class="full-width" color="pink-10">
                            <div class="text-white">
                            Rental Sekarang
                            </div>
                            </q-btn>
                        </q-card-actions>
                        </q-card>
                </div>
            </div>
          <q-dialog v-model="dialog" v-if="dialog" position="left">
            <q-card style="width: 500px">
              <q-card-section>
                  <div class="text-h6">Detail Pemesanan</div>
              </q-card-section>
              <q-card-section style="max-height: 50vh;" class="scroll">
                   <div class="text-bold text-h6">{{ activeData.namapenyewa }}</div> Dengan Harga Rp.{{ activeData.harga}},-
                    <q-form class="q-mt-md">
                <q-form>
                <q-input filled type="number" class="q-mb-md" v-model="jumlah" label="Jumlah Pesanan: "/>
                Total Harga Yang harus Di Bayar Rp.{{ total }},-
                  <q-file color="pink" class="q-mt-md" v-model="image" label="Masukan Bukti Pembayaran">
                  <template v-slot:prepend>
                      <q-icon name="attach_file" />
                      </template>
                    </q-file>
                </q-form>
                </q-form>
              </q-card-section>
              <q-card-actions align="right">
                <q-btn flat label="Batal" v-close-popup/>
                <q-btn color="black" @click="prosesTransaksi" label="Proses"  />
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
      stars: 4,
      image: null,
      dialog: false,
      data: [],
      activeData: null,
      jumlah: 1
    }
  },
  created () {
    this.geData()
  },
  methods: {
    geData () {
      this.$axios.get('rental/getall')
        .then(res => {
          if (res.data.sukses) {
            this.data = res.data.data.map(rental => {
              return Object.assign(rental, {
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
        idRental: this.activeData._id,
        harga: this.activeData.harga,
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
      return this.activeData.harga * this.jumlah
    }
  }
}
</script>