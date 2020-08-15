<template>
    <div class="container">
        <StreamBarcodeReader
            @decode="onDecode"
            @loaded="onLoaded"
        ></StreamBarcodeReader>
    </div>
</template>

<script>

    import { StreamBarcodeReader } from "vue-barcode-reader";
    export default {

        mounted() {

        },
        methods: {
            onDecode (result) {
                console.log(result)
            }
        }
    }

</script>
