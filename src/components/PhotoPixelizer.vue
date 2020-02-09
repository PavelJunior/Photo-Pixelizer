<template>
    <div>
        <div class="bar">
            <div>
                <span>Load your image: </span>
                <input type="file" id="imageLoader" name="imageLoader" @change="loadImage"/>
            </div>
            <div>
                <span>Number of pixels: </span>
                <input type="range" min="0.01" max="0.2" step="0.01" :value="scale" @input="scaleChanged">
            </div>
            <div>
                <span>Download pixel image: </span>
                <button><a href="#" download="image.png" @click="saveImage">Save</a></button>
            </div>
        </div>
        <div id="image-container">

        </div>
    </div>
</template>

<script>
    export default {
        name: "PhotoPixelizer",
        data(){
            return {
                scale: 0.1,
                image: new Image(),
                canvasCopy: null,
                contextCopy: null,
                canvasOriginal: null,
                contextOriginal: null
            }
        },
        methods: {
            saveImage(e){
                let image = this.canvasCopy.toDataURL("image/png");
                e.target.href = image;
            },
            scaleChanged(e){
                this.scale = e.target.value;
                this.converImage();
            },
            loadImage(e){
                let reader = new FileReader();
                reader.onload = event => this.readerOnload(event);
                reader.readAsDataURL(e.target.files[0]);

            },
            readerOnload(e){
                this.image = new Image();
                this.image.src = e.target.result;
                setTimeout(this.converImage, 100);
            },
            converImage(){
                let [width, height] = this.getImageDimentions;
                if(this.canvasCopy === null){
                    this.canvasOriginal = document.createElement('canvas');
                    this.contextOriginal = this.canvasOriginal.getContext('2d');

                    this.canvasCopy = document.createElement('canvas');
                    this.contextCopy = this.canvasCopy.getContext('2d');
                }

                this.canvasOriginal.width = width;
                this.canvasOriginal.height = height;

                this.canvasCopy.width = width;
                this.canvasCopy.height = height;

                this.contextCopy.imageSmoothingEnabled = false;

                let scaledWidth = width * this.scale;
                let scaledHeight = height * this.scale;

                this.contextOriginal.fillStyle = "#e7e7e7";
                this.contextOriginal.fillRect(0, 0, width, height);
                this.contextOriginal.drawImage(this.image, 0, 0, width, height);

                this.contextCopy.fillStyle = "#e7e7e7";
                this.contextCopy.fillRect(0, 0, width, height);
                this.contextCopy.drawImage(this.image, 0, 0, scaledWidth, scaledHeight);
                this.contextCopy.drawImage(this.canvasCopy , 0, 0, scaledWidth, scaledHeight, 0, 0, width, height);


                document.getElementById('image-container').appendChild(this.canvasOriginal);
                document.getElementById('image-container').appendChild(this.canvasCopy);
            }
        },
        computed:{
            getImageDimentions(){
                let width = this.image.width,
                    height = this.image.height;
                let potentialWidth = window.innerWidth/2-20,
                    potentialHeight = window.innerHeight-100;

                while(width > potentialWidth || height > potentialHeight){
                    width *= 0.9;
                    height *= 0.9;
                }

                return [width, height];
            }
        },
        mounted(){
            this.image.src = 'src/img/orange.jpg';
            this.image.onload = this.converImage;
        }
    }
</script>

<style>
    .bar{
        height: 70px;
        display: flex;
        background-color: gray;
        justify-content: center;
        align-items: center;
    }

    .bar div{
        margin-right: 10vw;
    }

    .bar div span{
        color: #000;
        font-size: 20px;
        margin-right: 10px;
    }

    #imageLoader{
        width: 100px;
    }

    #image-container{
        margin-top: 20px;
        display: flex;
        justify-content: space-around;
    }

    body{
        background-color: #e7e7e7;
    }

    a, a:hover{
        text-decoration: none;
        color: black;
        text-underline-mode: none;
        padding: 5px;
        margin: -5px;
    }
</style>