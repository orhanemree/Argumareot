<template>
    <div class="flex items-center justify-center gap-12 flex-col md:flex-row">
        <div id="camera" class="relative">
            <button class="absolute bottom-[-1.5rem] left-0 right-0 z-[1] m-auto bg-black rounded-[50%] w-12 h-12 border-2 border-black" @click="capture()">
                <img src="../assets/camera_FILL0_wght400_GRAD0_opsz48.svg" alt="camera">
            </button>
            <div class="z-[1] absolute top-3 left-3">
                <span v-show="motif.name" class="text-lg font-bold bg-[#f6f8fa] px-2">{{motif.name}}</span>
                <!-- <span>{{confidence}}</span> -->
            </div>
            <select @change="changeVideoSource($event)" class="z-[1] absolute top-3 right-3 bg-[#f6f8fa] rounded" name="devices">
                <option>Kamerayı Değiştir</option>
                <option v-for="device in devices" :key="device" :value="device.deviceId">{{device.label}}</option>
            </select>
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
            devices: []
        }
    },
    methods: {
        capture(){
            const dataUrlImage = document.querySelector(".p5Canvas").toDataURL('image/jpeg');
            console.log(dataUrlImage);
        },
        listDevices(){
            navigator.mediaDevices.enumerateDevices().then( devices => {
                    this.devices = devices.filter(device => device.kind === "videoinput");
                }
            );
        },
        changeVideoSource(e){
            window.localStorage["videoSourceDevice"] = e.target.value;
            location.reload();
        }
    },
    mounted(){
        this.listDevices();
        let classifier, videoInput, outputWidth, outputHeight, videoSource;
        const classifyVideo = () => {
            classifier.classify(videoInput, (err, result) => {
                if (err) throw err;
                if (result[0].confidence * 100 > 95){
                    this.motif = data.filter(m => m.name === result[0].label)[0];
                } else {
                    this.motif = {}
                }
                classifyVideo();
            });
        }
        const script = p => {
            p.preload = _ => {
                const modelUrl = "https://teachablemachine.withgoogle.com/models/7Jmpuujsv/model.json";
                classifier = ml5.imageClassifier(modelUrl);
                if (window.localStorage["videoSourceDevice"]){
                    videoSource = {
                        video: {
                            deviceId: window.localStorage["videoSourceDevice"]
                        }
                    }
                } else {
                    videoSource = p.VIDEO;
                }
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

                videoInput = p.createCapture(videoSource);
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