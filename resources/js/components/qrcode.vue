<template>
    <div>
        <p hidden class="error">{{ error }}</p>

        <!--TODO: comment or delete line below:-->
        <p hidden class="decode-result">Resultado: <b id="resultado">{{ result }}</b></p>
        <qrcode-stream @decode="onDecode" @init="onInit"/>
        <input hidden type="text" id="qrcode" name="qrcode" :value="result">

    </div>
</template>

<script>
    import { QrcodeStream } from 'vue-qrcode-reader'

    export default {
        // el:'#qrcode',
        components: { QrcodeStream },

        data () {
            return {
                result: '',
                error: '',
                camera: 'auto',
            }
        },

        methods: {
            onDecode (result) {
                console.log("decoding...");
                if (/(http|https):\/\/[a-zA-Z0-9]+/ig.test(result)) { //TODO: improve url recognition, right now it allows any 'http://...' or 'https://...' stuff
                    this.result = result;
                    // alert("BOOYAH!");
                    console.log("got a link!", result);


                    // alert(result);
                    document.forms.item(0).qrsubmit.hidden = "false";
                    document.forms.item(0).qrcode.value = result;
                    let i = setInterval(()=> {
                        if(document.forms.item(0).qrcode.value !== "" && /(http|https):\/\/[a-zA-Z0-9]+/ig.test(document.forms.item(0).qrcode.value)) {
                            document.forms.item(0).submit();
                            clearInterval(i);
                        }
                    }, 100);

                    // fetch(`${document.location.origin}/logista/finalizarSeparacao/${document.location.pathname.split("/").pop()}`,
                    //     {
                    //         method: 'POST',
                    //         headers: {
                    //             'Content-type': 'application/json',
                    //
                    //         },
                    //         body: JSON.stringify({
                    //             qrcode: $('#qrcode')[0].value
                    //         })
                    //     }
                    // ).then(response => {
                    //     window.document.location = URL.createObjectURL(response.blob())
                    // });
                    //TODO: execute outsider ghost function on js wrappable by laravel blades, so u can do omoshiroi things with sensivite routes used in this application
                }else{
                    // fetch(`${document.location.origin}/rand`).then(response => window.open(window.URL.createObjectURL(response.blob()), "_blank"));
                    // this.result = "";
                    console.log("It's not a link bro!");
                    // alert("Dub");
                    // console.log("Dub");
                }
            },

            async onInit (promise) {
                try {
                    await promise
                } catch (error) {
                    if (error.name === 'NotAllowedError') {
                        this.error = "ERROR: Você não aceitou os termos de uso de camera"
                    } else if (error.name === 'NotFoundError') {
                        this.error = "ERROR: no camera on this device"
                    } else if (error.name === 'NotSupportedError') {
                        this.error = "ERROR: secure context required (HTTPS, localhost)"
                    } else if (error.name === 'NotReadableError') {
                        this.error = "ERROR: is the camera already in use?"
                    } else if (error.name === 'OverconstrainedError') {
                        this.error = "ERROR: Camera não suportada"
                    } else if (error.name === 'StreamApiNotSupportedError') {
                        this.error = "ERROR: Stream API is not supported in this browser"
                    }
                }
            }
        }
    }
</script>

<style scoped>
    .error {
        font-weight: bold;
        color: red;
    }
</style>
