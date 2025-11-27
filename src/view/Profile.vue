<script setup>
import { ref } from 'vue'
import BottomBar from '../components/BottomBar.vue';
import axios from 'axios'
import { useRouter } from 'vue-router';
import Swal from 'sweetalert2'

const router = useRouter()

const user = JSON.parse(localStorage.getItem('user'))
const data = ref({})

const signout = () => {
    Swal.fire({
        title: 'Keluar',
        text: "Anda yakin ingin keluar ?",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        cancelButtonText: 'Tidak',
        confirmButtonText: 'Ya, keluar!'
    }).then(async (result) => {
        if (result.isConfirmed) {
            
            await axios.post(`${import.meta.env.VITE_BASE_URL}/api/logout`, {}, {
                headers: {
                    Authorization: 'Bearer ' + localStorage.getItem('token')
                }
            })
            
            localStorage.removeItem('user')
            localStorage.removeItem('token')

            router.push('/login')
        }
    })
}

const update = () => {
    Swal.fire({
        title: 'Ubah Profil',
        text: "Anda yakin ingin mengubah profil ?",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        cancelButtonText: 'Tidak',
        confirmButtonText: 'Ya, ubah!'
    }).then(async (result) => {
        if (result.isConfirmed) {
            
            const send = await axios.post(`${import.meta.env.VITE_BASE_URL}/api/profile/${user.id}`, user, {
                headers: {
                    Authorization: 'Bearer ' + localStorage.getItem('token')
                }
            })

            if(send.status == 200){
                Swal.fire({
                    title: 'Berhasil',
                    text: 'Profil berhasil diubah',
                    icon: 'success',
                    showConfirmButton: false,
                    timer: 1000
                })
                localStorage.setItem('user', JSON.stringify(send.data.user))

                router.push('/profile')
            }
            else{
                Swal.fire({
                    title: 'Gagal',
                    text: 'Profil gagal diubah',
                    icon: 'error',
                    showConfirmButton: false,
                    timer: 1000
                })
            }
        }
    })
}
</script>

<template>
    <div class="container">
        <!-- <div class="d-flex align-items-center px-1 mt-4">
            <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRCk3j9rnPG4OKDmiA0Ck8sfmC4poGsZDgyqQ&s" alt="profile" class="img-fluid object-fit-cover rounded-circle me-3" width="48" height="48">
            <div>
                <h6 class="fw-bold p-0 m-0 pt-2">Rafi Ahfa</h6>
                <span class="text-muted mb-0 fs-13">rafiahfa01@gmail.com</span>
            </div>
        </div> -->

        <div class="mt-5 mb-4 text-center">
            <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRCk3j9rnPG4OKDmiA0Ck8sfmC4poGsZDgyqQ&s" alt="profile" class="img-fluid object-fit-cover rounded-circle" width="80" height="80">
            <h6 class="fw-bold p-0 m-0 pt-3">{{ user.name }}</h6>
            <span class="text-muted mb-0 fs-13">{{ user.email }}</span>
        </div>

        <div class="col-12 bg-light text-dark px-3 py-2 rounded mt-2">
            <div class="d-flex align-items-center pt-2">
                <h6 class="fs-13 fas fa-sync me-2"></h6>
                <h6 class="fs-13">Sinkronisasi Data (678 kb tersedia)</h6>
            </div>
        </div>
        <div class="col-12 bg-light text-dark px-3 py-2 rounded mt-2" data-bs-toggle="modal" data-bs-target="#myModal">
            <div class="d-flex align-items-center pt-2">
                <h6 class="fs-13 fas fa-address-card me-2"></h6>
                <h6 class="fs-13">Ubah Profil</h6>
            </div>
        </div>
        <div class="col-12 bg-danger text-white px-3 py-2 rounded mt-2" @click="signout">
            <div class="d-flex align-items-center pt-2">
                <h6 class="fs-13 fas fa-sign-out-alt me-2"></h6>
                <h6 class="fs-13 fw-bold">Sign Out</h6>
            </div>
        </div>

        <div class="modal fade" id="myModal" tabindex="-1" aria-labelledby="myModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable px-3">
                <div class="modal-content">
                    <div class="modal-header border-0">
                        <h6 class="modal-title" id="myModalLabel">Ubah Profil</h6>
                        <!-- <button type="button" class="btn-close fs-13" data-bs-dismiss="modal" aria-label="Tutup"></button> -->
                    </div>
                    <div class="modal-body">
                        <form id="modalForm" class="fs-13">
                            <div class="mb-2">
                                <label for="nama" class="form-label">Nama</label>
                                <input v-model="user.name" type="text" class="form-control fs-13" id="nama" placeholder="Masukkan nama" required>
                            </div>
                            <div class="mb-2">
                                <label for="email" class="form-label">Email</label>
                                <input v-model="user.email" type="email" class="form-control fs-13" id="email" placeholder="john@mail.to" required>
                            </div>
                            <div class="mb-2">
                                <label for="text" class="form-label">Password</label>
                                <input v-model="user.password" type="text" class="form-control fs-13" id="password" placeholder="Kosongkan jika tidak ingin mengubah">
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer border-0">
                        <button type="button" class="btn btn-secondary fs-13" data-bs-dismiss="modal">Batal</button>
                        <button type="button" id="saveBtn" class="btn btn-primary fs-13" @click="update">Simpan</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
    <BottomBar />
</template>

<style scoped>
.fs-13{
    font-size: 13px;
}
.fs-21{
    font-size: 11px;
}
</style>