<template>
  <v-container>
    <v-responsive class="d-flex align-center text-center">
      <div class="d-flex justify-center">
        <p class="text-h3 font-weight-bold">Web&nbsp;to&nbsp;iOS</p>
        <p class="text-caption" align="right">by Krishapps</p>
      </div>

      <div class="py-2" />
      
      <div class="text-body-2 font-weight-light mb-n1">
        Üdvözlünk a Krishapps Web to iOS konvertálójában. Ezzel a konvertálóval könnyedén konvertálhatsz weboldalakat alkalmazásokká. 
      </div>

      <div class="py-2" />
      <v-text-field 
        label="App neve"
        v-model="label"
        density="compact"
        placeholder="Az én alkalmazásom"
        hint="Az alkalmazás neve, amit a kezdőképernyőn is láthatsz. "
      ></v-text-field>
      <v-text-field 
        label="Id"
        v-model="id"
        density="compact"
        hint="A webklip egyedi azonosítója. Ugyanezzel az azonosítóval lecserélhetsz egy meglévő alkalmazást."  
      ></v-text-field>
      <v-text-field 
        label="Url"
        v-model="url"
        density="compact"
        placeholder="https://pelda.hu vagy akár messages://"
        hint="Az URL ami megjelenik az alkalmazásban. NE használj szóközt!"
        @focus="scrollIntoView"
      ></v-text-field>
      <v-checkbox 
        label="Előre komponált (ne módosítsa az ikont)"
        v-model="precomposed"
        density="compact"
        hide-details="true"
      ></v-checkbox>
      <v-checkbox 
        label="Safari felhasználói felület kikényszerítése"
        v-model="ignoreManifestScope"
        density="compact"
      ></v-checkbox>
      <ImageCmp :image="image"/>
      <v-btn
        @click="generate"
      >
        Generálás
      </v-btn>

      <div class="py-2" />
    </v-responsive>
  </v-container>
</template>

<script setup>
import {ref} from 'vue'
import { generateWebClip } from '@/helper/webcliptemplate.js'
import { v4 as uuidv4 } from 'uuid'
import ImageCmp from '@/components/ImageCmp.vue'
import { useAppStore } from '@/store/app.js'

const store = useAppStore()

const label = ref('')
const id = ref(uuidv4())
const url = ref('')
const image = ref({})
const precomposed = ref(true)
const ignoreManifestScope = ref(false)


const generate = () => {
  if (!label.value) {
    store.showSnackbar('Hiányzó címke!')
    return
  }
  if (!id.value) {
    store.showSnackbar('Hiányzó ID!')
    return
  }
  if (!url.value  || !url.value === '') {
    store.showSnackbar('Hiányzó vagy nem megfelelő URL!')
    return
  }
  if (!image.value  || !image.value.data) {
    store.showSnackbar('Hiányzó ikon!')
    return
  }
  const clip = {
    payloadId: id.value,
    payloadUUID: id.value,
    label: label.value,
    url: url.value,
    image: image.value.data,
    precomposed: precomposed.value,
    ignoreManifestScope: ignoreManifestScope.value,
  }
  const webclip = generateWebClip(clip)
  const link = document.createElement('a')
  link.download = label.value
  link.href = window.URL.createObjectURL(webclip)
  link.click()
}

const scrollIntoView = (event) => {
  event.target.scrollIntoView()
}
</script>
