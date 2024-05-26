<template>
  <q-page class="flex flex-center">
    <div>
      <q-btn
        label="Print"
        @click="setPrint"
        class="q-ma-md"
        />
        <q-btn
        label="Devise"
        @click="inputDeviceInfo"
        class="q-ma-md"
      />
        <q-btn
        label="Geo"
        @click="getCurrentPosition"
        class="q-ma-md"
      />
      <q-input
        v-model="textValue"
        filled
        type="textarea"
      />
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import { BluetoothLe } from '@capacitor-community/bluetooth-le';
import { Device } from '@capacitor/device'
import { Geolocation } from '@capacitor/geolocation'

export default defineComponent({
  name: 'IndexPage',
  data() {
    return {
      textValue: '',
      cnt: 0,
    }
  },

  methods: {
    log(text) {
      // const len = this.textValue.split('---');
      this.textValue = [`${++this.cnt}) ${text}`, this.textValue].join('\n---\n');

    },
    async setPrint() {
      try {
        // Ensure Bluetooth is initialized and connected to the printer
        await BluetoothLe.initialize();
        await BluetoothLe.connect({
          deviceId: '11:22:33:44:55:66',
          // Use the UUID provided in the documentation
          services: ["00001101-0000-1000-8000-00805F9B34FB"]
        });

        // Assuming the characteristic UUID for writing data is the same as the service UUID
        const serviceUUID = '00001101-0000-1000-8000-00805F9B34FB';
        const characteristicUUID = '00001101-0000-1000-8000-00805F9B34FB';

        await BluetoothLe.write({
          deviceId: '11:22:33:44:55:66',
          service: serviceUUID,
          characteristic: characteristicUUID,
          value: btoa('Hello world!') // Convert text to base64
        });

        this.log("Printed: Hello world!");
      } catch (error) {
        this.log(error.message);
      }
    },
    inputDeviceInfo() {
      Device.getInfo().then(info => {
        this.log(`Device info: ${JSON.stringify(info, null, 2)}`);
      }).catch(error=>{
        this.log(error.message);
      });
    },
    getCurrentPosition() {
      Geolocation.getCurrentPosition().then(newPosition => {
        this.log(`Current ${newPosition}`, newPosition)
        // position.value = newPosition
      }).catch(error=>{
        this.log(error.message);
      });
    },
  },
  mounted() {
    this.inputDeviceInfo()
  }
})
</script>
