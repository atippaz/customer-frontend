<template>
  <v-container class="mt-12">
    <a
    class="btn btn-primary"
    :href="urlFile"
    target="_blank"
    :download="fileName"
    style="text-decoration: underline"
  >
    openfile
  </a>
    <div class="d-flex justify-space-between ga-2">
      <div>
        <v-btn color="success" @click="download">GetCustomer Generate</v-btn>
      </div>
      <div class="d-flex justify-end ga-2">
        <v-btn color="success" @click="openfile">import Data</v-btn>
        <v-btn color="primary" @click="dialog = true">create Data</v-btn>
      </div>
    </div>
    <v-card class="mt-4">
      <v-data-table
        class="elevation-4"
        :items="items"
        hide-actions
        select-all
        pagination.sync="pagination"
        :loading="loading"
      >
      </v-data-table>
    </v-card>
    <v-dialog
      v-model="dialog"
      scrollable
      persistent
      location="center center"
      :overlay="false"
    >
      <v-card>
        <v-card-title primary-title> title </v-card-title>
        <v-container>
          <v-row>
            <v-col>
              <div>
                <v-select
                  v-model="customerTypeId"
                  label="customer type"
                  :items="customerType"
                  item-title="customerTypeName"
                  item-value="customerTypeId"
                ></v-select>
                <v-select
                  v-model="customerPrefixId"
                  label="customer Prefix"
                  :items="customerPrefix"
                  item-title="customerPrefixName"
                  item-value="customerPrefixId"
                ></v-select>
                <v-text-field
      label="id card"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
                <v-text-field
      label="customer firstname"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
    <v-text-field
      label="customer lastname"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
              </div>
              <div>
                phone section
                email section
              </div>
            </v-col>
            <v-col>
              <div>
                <v-text-field
      label="address"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
    <v-text-field
      label="moo"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
    <v-text-field
      label="building"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
    <v-text-field
      label="soi"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
    <v-text-field
      label="road"
      :rules="rules"
      hide-details="auto"
    ></v-text-field>
               
              </div>
            </v-col>
            <v-col>
<div>
  <v-select
                  label="province"
                  :items="provinces"
                  v-model="provinceId"
                  item-title="provinceNameTh"
                  item-value="provinceId"
                ></v-select>

                <v-select
                  label="district"
                  :disabled="provinceId == null"
                  :items="districts.filter((x) => x.provinceId == provinceId)"
                  v-model="districtId"
                  item-title="districtNameTh"
                  item-value="districtId"
                ></v-select>
                <v-select
                  label="sub district"
                  :disabled="districtId == null"
                  :items="
                    subDistricts.filter((x) => x.districtId == districtId)
                  "
                  v-model="subDistrictId"
                  item-title="subDistrictNameTh"
                  item-value="subDistrictId"
                ></v-select>
                postcode:{{ subDistrictObj }}
</div>
            </v-col>
          </v-row>
        </v-container>
        <v-card-actions class="d-flex justify-end">
          <v-btn color="error" @click="closeDialog">cancel</v-btn>
          <v-btn color="success">submit</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <input class="file-btn" ref="browseFile" type="file" />
  </v-container>
 </template>

<script setup>
import { watch } from "vue";
import { computed } from "vue";
import { ref } from "vue";
const browseFile = ref(null)
const file = ref(null)
const apiPath = "http://localhost:5255";
const customerApi = `${apiPath}/customer`;
const masterDataApi = `${apiPath}/masterData`;
const customerPrefix = ref([]);
const customerType = ref([]);
const customerTypeId = ref(null);
const customerPrefixId = ref(null);
const dialog = ref(false);
const loading = ref(false);
const items = ref([]);
const provinces = ref([]);
const districts = ref([]);
const subDistricts = ref([]);
const provinceId = ref(null);
const districtId = ref(null);
const subDistrictId = ref(null);
const urlFile = ref('');
const rules= [
        value => !!value || 'Required.',
        // value => (value && value.length >= 3) || 'Min 3 characters',
      ]
watch(
  () => provinceId.value,
  () => {
    districtId.value = null;
  }
);
watch(
  () => districtId.value,
  () => {
    subDistrictId.value = null;
  }
  );
  function closeDialog(){
    dialog.value = false
  }
  function saveData(data,fileName) {
    var a = document.createElement("a");
    document.body.appendChild(a);
    a.style = "display: none";
            var blob = new Blob([data], {type: "octet/stream"});
            console.log(blob);
            var url = window.URL.createObjectURL(blob);
        a.href = url;
        a.download = fileName;
        a.click();
        window.URL.revokeObjectURL(url);
}
  function download(){
    fetch(customerApi+'/GenarateRandomData', {
  })
    .then((e) => e.blob())
    .then((res) => {
      saveData(res, "test.xlsx")
    })
  }
function openfile() {
console.log(browseFile.value)
  browseFile.value.onchange = (e) => {
      if (e.target) {
        const file  = e.target.files[0]
        // addFile(file)
        const data = new FormData()
        // fileList.value.forEach((file) => {
        //   console.log(file)
        // })
        data.append('Attachments', file, 'file')
         fetch(customerApi+'/import', {
    method: 'POST',
    body: data
  })
    .then((e) => e.json())
    .then((e) => {console.log(e)
    getAllCustomer()})
    .catch((e) => console.log(e))
      }
    }
  browseFile.value.click()
}
async function downloadFile(file = null) {
  if (await file) {
    console.log(await file)

    // const name = file.name + ':123'
    // const f = new File([file], name)

    fileList.value.push(file)
  }
}
const subDistrictObj = computed(() => {
  const subdistrictSelect = subDistricts.value.find(
    (x) => x.subDistrictId == subDistrictId.value
  );
  return subdistrictSelect ? subdistrictSelect.postCode : "";
});
async function getProvince() {
  await fetch(`${masterDataApi}/GetProvince`)
    .then((e) => e.json())
    .then((e) => {
      e.statusCode === 200
        ? (provinces.value = e.data)
        : (provinces.value = []);
      console.log(e);
    })
    .catch((e) => console.log(e));
}
async function getCustomerPrefix() {
  await fetch(`${customerApi}/GetCustomerPrefix`)
    .then((e) => e.json())
    .then((e) => {
      e.statusCode === 200
        ? (customerPrefix.value = e.data)
        : (customerPrefix.value = []);
      console.log(e);
    })
    .catch((e) => console.log(e));
}
async function getCustomerType() {
  await fetch(`${customerApi}/GetCustomerType`)
    .then((e) => e.json())
    .then((e) => {
      e.statusCode === 200
        ? (customerType.value = e.data)
        : (customerType.value = []);
      console.log(e);
    })
    .catch((e) => console.log(e));
}
async function getDistrict() {
  await fetch(`${masterDataApi}/GetDistrict`)
    .then((e) => e.json())
    .then((e) => {
      e.statusCode === 200
        ? (districts.value = e.data)
        : (districts.value = []);
      console.log(e);
    })
    .catch((e) => console.log(e));
}
async function getSubDistrict() {
  await fetch(`${masterDataApi}/GetSubDistrict`)
    .then((e) => e.json())
    .then((e) => {
      e.statusCode === 200
        ? (subDistricts.value = e.data)
        : (subDistricts.value = []);
      console.log(e);
    })
    .catch((e) => console.log(e));
}
async function getAllCustomer() {
  await fetch(`${customerApi}/index`)
    .then((e) => e.json())
    .then((e) => {
      e.statusCode === 200 ? (items.value = e.data) : (items.value = []);
      console.log(e);
    })
    .catch((e) => console.log(e));
}
(async () => {
  loading.value = true
  await getProvince()
  await getDistrict()
  await getSubDistrict()
  await getCustomerPrefix()
  await getCustomerType()
  await getAllCustomer()
  loading.value = false
})();
</script>
<style scoped>
.file-btn {
  visibility: hidden;
  z-index: -1;
  position: absolute;
}</style>
