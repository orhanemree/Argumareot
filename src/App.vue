<template>
    <Camera />
    <button class="z-[2] absolute md:top-4 bottom-4 right-4 w-12 h-12 bg-orange-400 rounded hover:bg-orange-300 transition" @click="toggleMenu">
        <img src="./assets/menu_FILL0_wght400_GRAD0_opsz48.svg" alt="menu">
    </button>
    <div v-show="menu" class="p-10 gap-5 absolute inset-0 z-[1] w-screen h-screen bg-[#f6f8fa] flex flex-col">
        <button class="menu-item" @click="scan()">Motif Tara</button>
        <button class="menu-item" @click="toggleTeam()">Teknofest Takımı</button>
        <a class="menu-item gap-2" href="https://github.com/orhanemree/Argumareot">
            <img class="w-7" src="./assets/github.svg" alt="github">
            <span>GitHub</span>
        </a>
    </div>
    <div v-show="team" class="absolute inset-0 z-[1] w-screen h-screen bg-[#f6f8fa] flex flex-col p-8">
        <h1 class="font-bold text-2xl text-center">Teknofest Turizm Teknolojileri Yarışması Argumareot Takımı</h1>
            <hr class="m-5">
            <div class="grid sm:grid-cols-1 grid-cols-2 gap-4">
                <div class="hidden sm:grid-item text-lg font-bold">
                    <div>Takım Üyesi</div>
                    <div>Okulu</div>
                    <div>Takımdaki Görevi</div>
                </div>
                <div class="grid-item">
                    <div><a href="mailto:gokcenazbalci@hotmail.com">Gökçe Naz Balcı</a></div>
                    <div>Mekteb-i İdadi-i Mülki Afyon Lisesi</div>
                    <div>Takım Kaptanı</div>
                </div>
                <div class="grid-item">
                    <div>Berra Ulukaya</div>
                    <div>Kamil Miras Anadolu Lisesi</div>
                    <div>Veri Toplama</div>
                </div>
                <div class="grid-item">
                    <div>Rümeysa Esma Karanfil</div>
                    <div>Afyon Süleyman Demirel Fen Lisesi</div>
                    <div>Veri Toplama</div>
                </div>
                <div class="grid-item">
                    <div><a href="https://www.instagram.com/arda.canm/">Arda Can</a></div>
                    <div>Mekteb-i İdadi-i Mülki Afyon Lisesi</div>
                    <div>Araştırma</div>
                </div>
                <div class="grid-item">
                    <div><a href="https://github.com/orhanemree">Orhan Emre Dikicigil</a></div>
                    <div>TED Afyon Koleji</div>
                    <div>Web Geliştirme</div>
                </div>
            </div>
        <div class="absolute bottom-3 left-0 text-center w-full font-bold">MIT Lisansı &copy;</div>
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