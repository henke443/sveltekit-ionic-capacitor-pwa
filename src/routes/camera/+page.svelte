<script lang="ts">

import { Camera, CameraResultType } from '@capacitor/camera';

let imageUrl = ""

const takePicture = async () => {
  const image = await Camera.getPhoto({
    quality: 90,
    allowEditing: true,
    resultType: CameraResultType.Uri
  });
  imageUrl = image.webPath ?? "";
};

</script>
<ion-card>

{#if imageUrl}
    <img src={imageUrl} alt="" />
{:else}
    <p>No image</p>
{/if}

    Here content

<ion-button role="button" tabindex="0" on:keydown={takePicture} on:click={takePicture} >Take pic</ion-button>    
</ion-card>
