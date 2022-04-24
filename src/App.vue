<template>
    <Camera />
    <button class="z-[2] absolute md:top-4 bottom-4 right-4 w-12 h-12 bg-orange-400 rounded hover:bg-orange-300 transition" @click="toggleMenu">
        <img src="./assets/menu_FILL0_wght400_GRAD0_opsz48.svg" alt="menu">
    </button>
    <div v-show="menu" class="p-10 gap-5 absolute inset-0 z-[1] w-screen h-screen bg-[#f6f8fa] flex flex-col">
        <button class="menu-item" @click="scan()">Motif Tara</button>
        <button class="menu-item" @click="toggleTeam()">Teknofest Takımı</button>
        <div class="menu-item gap-2">
            <img class="w-7" src="./assets/github.svg" alt="github">
            <a href="https://github.com/orhanemree/Argumareot">GitHub</a>
        </div>
    </div>
    <div v-show="team" class="absolute inset-0 z-[1] w-screen h-screen bg-[#f6f8fa] flex flex-col">
        <!-- TODO: Takım bilgilerini yaz -->
        Buraya takım ile ilgili bilgilendirmeler gelecek.
    </div>
</template>

<script>
import Camera from "./components/Camera.vue";
export default {
    data(){
        return{
            menu: false,
            team: false
        }
    },
    components: {
        Camera,
    },
    methods: {
        toggleMenu(){
            this.menu = !this.menu;
            this.team = false;
        },
        scan(){
            this.menu = false;
            this.team = false;
        },
        toggleTeam(){
            this.menu = false;
            this.team = true;
        }
    }
}
</script>