<script lang="ts">
    import { Cloudinary } from "@cloudinary/url-gen";
    import { backgroundRemoval } from "@cloudinary/url-gen/actions/effect";
    import { ImageStatus } from "./types.d";
    import { imageStatus, modifiedImage, originalImage } from "./store";
    import Dropzone from "dropzone";
    //import 'drowzone/dist/dropzone.css'

    import { onMount } from "svelte";

    const cloudinary = new Cloudinary({
        cloud: {
            cloudName: "djbx6vefv",
        },
        url: {
            secure: true, // force https, set to false to force http
        },
    });

    onMount(() => {
        const dropzone = new Dropzone("#dropzone", {
            uploadMultiple: false,
            acceptedFiles: ".jpg,.png,.wepp",
            maxFiles: 1,
        });
        dropzone.on("sending", (file, xhr, formData) => {
            imageStatus.set(ImageStatus.UPLOADING);
            //ADD APIkEY AND CONFIG
            formData.append('upload_preset', 'cu4xwnnj');
            formData.append('timestamp', (Date.now() / 1000));
            formData.append('api_key', '711324235435561');
        });

        dropzone.on("success", (file, response) => {
            const {public_id: publicId,secure_url:url}=response

              //create the image width background trasparent
            // ang save image
            const imagewithoutBackground=cloudinary
                .image(publicId)
                .effect(backgroundRemoval())
            console.log()
            console.log(imagewithoutBackground.toURL())

            imageStatus.set(ImageStatus.DONE);
            modifiedImage.set(imagewithoutBackground.toURL())
            originalImage.set(url);
          
        });
        dropzone.on("error", (file, response) => {
            console.log("nottt.. fake");
            console.log(response);
        });
    });
</script>

<form
    action="https://api.cloudinary.com/v1_1/djbx6vefv/image/upload"
    id="dropzone"
    class="shadow-2xl border-dished border-2 border-gray-300 rounded-lg aspect-video 
            flex  justify-center item-center  flex-col"
>
    {#if $imageStatus === ImageStatus.READY}
        <button
            class="pointer-events-none mx-auto bg-blue-600 rounded-full text-bold text-white text-xl px-6 py-4"
        >
            Upload File
        </button>
        <strong class="text-center font-fold font-lg">or drop a file</strong>
    {:else if $imageStatus === ImageStatus.UPLOADING}
        <strong class="text-center font-fold font-lg">Uploading Files...</strong
        >
    {/if}
</form>
