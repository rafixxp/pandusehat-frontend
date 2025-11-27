<script setup>
import BottomBar from '../components/BottomBar.vue'
import { ref, onBeforeMount } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const router = useRouter()

const user = JSON.parse(localStorage.getItem('user'));
const rekomendasi = ref('');
const token = localStorage.getItem('token')

const progress = ref([]);

const detail = (id) => {
    router.push(`/progress/detail/${id}`)
}

onBeforeMount(async () => {
    try {
        const res = await axios.get(`${import.meta.env.VITE_BASE_URL}/api/progress`, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        })

        const rekomendasiRes = await axios.get(`${import.meta.env.VITE_BASE_URL}/api/rekomendasi`, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        })

        rekomendasi.value = rekomendasiRes.data

        progress.value = res.data
    }
    catch(error){
        console.log(error);
    }
})

</script>

<template>
    <div class="container">
        <div class="row pt-4 bg-white px-1 sticky-top ">
            <div class="col-9">
                <h6 class="fw-bold p-0 m-0 mt-2">Hi, {{ user.name }} !</h6>
                <p class="text-muted fs-21">Bagaimana kabarmu hari ini ?</p>
            </div>
            <div class="col-3 text-end">
                <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRCk3j9rnPG4OKDmiA0Ck8sfmC4poGsZDgyqQ&s" width="48" height="48" alt="profile" class="img-fluid object-fit-cover rounded-circle">
            </div>
        </div>

        <h6 class="fw-bold mb-3 px-1 fs-13 mt-3">Rekomendasi</h6>
        <div :class="rekomendasi.z_score <= -3 || rekomendasi.z_score < -2 && rekomendasi.z_score > -3 ? 'bg-danger' : 'primary'" class="p-3 rounded mt-2 text-white">
            <span class="text-justify fs-13" v-html="rekomendasi.rekomendasi"></span>
        </div>
        
        <div class="bg-transparent mt-4 mb-5 pb-4 px-1">
            <h6 class="fw-bold mb-3 fs-13">Riwayat Pemeriksaan</h6>
            <div class="col-12 p-3 bg-white border rounded mt-2" v-for="pro in progress" @click="detail(pro.id)">
                <div class="row">
                    <div class="col-10">
                        <h6 class="fs-14 fw-bold m-0 p-0 pb-2">{{ pro.tgl }}</h6>
                        <p class="text-muted fs-21 p-0 mb-2">{{ pro.nama }} - {{ pro.umur }} Tahun</p>
                        <span class="fs-21 bg-primary text-white px-2 py-1 rounded me-1">Selesai</span>
                        <!-- <span class="fs-21 bg-danger text-white px-2 py-1 rounded">Obesitas</span> -->
                    </div>
                    <div class="col-2 text-end">
                        <h6 class="fas fa-chevron-right mt-4 pt-1 text-secondary fs-14"></h6>    
                    </div>
                </div>
            </div>
        </div>
    </div>
    <BottomBar />
</template>

<style scoped>
.bg-primary{
    background-color: #0048ffe9 !important;
}
.text-justify{
    text-align: justify !important;
}
.fs-14{
    font-size: 13px;
}
.fs-13{
    font-size: 12px;
}
.fs-21{
    font-size: 11px;
}
</style>