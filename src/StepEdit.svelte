<script lang="ts">
    import { image } from "@cloudinary/url-gen/qualifiers/source";
    import "two-up-element";
    import { originalImage, modifiedImage } from "./store";

    //el efecto
    let processImage = true;
    let tries = 0;
    let intervalId: any;
    $: {
        if (processImage) {
            clearInterval(intervalId);
            intervalId = setInterval(() => {
                tries++;
                const img = new Image();
                img.src = $modifiedImage;
                img.onload = () => {
                    processImage = false;
                    clearInterval(intervalId);
                };
            }, 500);
        }
    }
</script>

<!-- svelte-ignore a11y-img-redundant-alt -->
<two-up>
    <img src={$originalImage} alt="image original upload a cloaudinary" />
    {#if processImage}
        <div class="felx flex-col justify-center items-center">
            <div class="lds-ripple">
                <div />
                <div />
            </div>
            <p class="text-center mt-4">Procesando imagen</p>
        </div>
    {:else}
        <img src={$modifiedImage} alt="image modified for a cloaudinary" />
    {/if}
</two-up>

<a
    download
    href={$modifiedImage}
    class=" block bg-blue-500 text-xl text-center w-full font-bold text-white rounded-full px-4 py-2 hover:bg-blue-700 mt-4"
>
    descargar Imagen sin fondo
</a>
