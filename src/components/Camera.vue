<template>
    <div class="flex items-center justify-center gap-12 flex-col md:flex-row">
        <div id="camera" class="relative">
            <div class="z-[1] absolute top-3 left-3">
                <span v-show="motif.name" class="text-lg font-bold bg-[#f6f8fa] px-2">{{motif.title}}</span>
            </div>
            <div class="z-[1] absolute top-3 right-3 flex items-center justify-center gap-3">
                <select @change="changeVideoSource($event)" class="bg-[#f6f8fa] rounded sm:text-base text-xs" name="devices">
                    <option>Kamerayı Değiştir</option>
                    <option v-for="device in devices" :key="device" :value="device.deviceId">{{device.label}}</option>
                </select>
                <button @click="mirrorVideo()" class="bg-[#f6f8fa] rounded px-2">
                    <span class="sm:text-base text-xs">Aynala</span>
                    <img class="sm:h-6 h-2 inline" src="../assets/autorenew_FILL0_wght400_GRAD0_opsz48.svg" alt="mirror video">
                </button>
            </div>
        </div>
        <div class="md:w-64 w-[80%] mx-auto text-justify text-md">
            {{motif.info}}
        </div>
    </div>
</template>

<script>
import data from "../assets/data.json";
export default {
    name: "Camera",
    data(){
        return{
            motif: {},
            devices: [],
            storage: {}
        }
    },
    methods: {
        // kullanılabilir video kaynaklarının listele
        listDevices(){
            navigator.mediaDevices.enumerateDevices().then( devices => {
                    this.devices = devices.filter(device => device.kind === "videoinput");
                }
            );
        },

        // video kaynağını ve localstorage verisini değiştir
        changeVideoSource(e){
            this.storage.videoSource = {
                video: {
                    deviceId: e.target.value
                }
            }
            window.localStorage["argumareot-video"] = JSON.stringify(this.storage);
            location.reload();
        },

        // video aynalama değişkenini ve localstorage verisini değiştir
        mirrorVideo(){
            this.storage.isMirrored = !this.storage.isMirrored;
            window.localStorage["argumareot-video"] = JSON.stringify(this.storage);
            location.reload();
        }
    },
    mounted(){
        this.listDevices();

        // ml5 video classifier
        let classifier, videoInput, outputWidth, outputHeight;
        const classifyVideo = () => {
            classifier.classify(videoInput, (err, result) => {
                if (err) throw err;
                if (result[0].confidence * 100 > 95 && result[0].label !== "light" && result[0].label !== "dark"){
                    this.motif = data.filter(m => m.name === result[0].label)[0];
                } else {
                    this.motif = {}
                }
                classifyVideo();
            });
        }

        // p5 script
        const script = p => {
            p.preload = _ => {
                const modelUrl = "https://teachablemachine.withgoogle.com/models/EKbsPs3Cl/model.json";
                classifier = ml5.imageClassifier(modelUrl);

                // video ayalarını al (video kaynağı ve aynalama)
                if (!window.localStorage["argumareot-video"]){
                    window.localStorage["argumareot-video"] = JSON.stringify({ videoSource: p.VIDEO, isMirrored: false });
                }
                this.storage = JSON.parse(window.localStorage["argumareot-video"]);

            }
            p.setup = _ => {
                outputWidth = window.innerWidth / 2;
                outputHeight = outputWidth * 0.75;

                if (window.matchMedia("(max-width: 768px)").matches) {
                    outputWidth = window.innerWidth *  .9;
                    outputHeight = outputWidth * 0.75;
                }

                const canvas = p.createCanvas(outputWidth, outputHeight);
                canvas.parent("camera");
                if (this.storage.isMirrored) {
                    canvas.canvas.classList.add("-scale-x-100");
                }

                videoInput = p.createCapture(this.storage.videoSource);
                videoInput.size(outputWidth, outputHeight);
                videoInput.hide();

                classifyVideo();
            }
            p.draw = _ => {
                p.image(videoInput, 0, 0, outputWidth, outputHeight);
            }
        }
        new p5(script);
    }
}
</script>