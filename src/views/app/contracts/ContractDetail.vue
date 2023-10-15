<template>
  <div v-if="isLoading">
    <b-row class="app-row">
      <page-header :title="$t('contract.edit')">
        <div class="top-right-button-container">
          <b-button variant="primary" size="lg" class="top-right-button" @click.prevent="submitForm" :class="{
            'btn-multiple-state': true,
            'show-spinner': status === 'processing',
            'show-success': status === 'success',
            'show-fail': status === 'fail'
          }" :disabled="isProcessing != false">
            <span class="spinner d-inline-block">
              <span class="bounce1"></span>
              <span class="bounce2"></span>
              <span class="bounce3"></span>
            </span>
            <span class="icon success">
              <i class="simple-icon-check"></i>
            </span>
            <span class="icon fail">
              <i class="simple-icon-exclamation"></i>
            </span>
            <span class="label">
              {{ $t('pages.submit') }}
            </span>
          </b-button>
        </div>
      </page-header>
    </b-row>
    <contract-update-form :items="items" ref="updateForm" @update-vehicle="vehicleUpdate"></contract-update-form>
    <contract-application-menu v-if="isLoading" :items="items"></contract-application-menu>
  </div>
  <div v-else class="loading" key="itemLoading"></div>
</template>

<script>
import axios from 'axios'
import { apiUrl } from "../../../constants/config";
import PageHeader from '../../../components/Common/PageHeader';
import ContractApplicationMenu from "../../../containers/forms/ContractApplicationMenu";
import ContractUpdateForm from "../../../containers/forms/ContractUpdateForm"

export default {
  components: {
    "page-header": PageHeader,
    "contract-application-menu": ContractApplicationMenu,
    "contract-update-form": ContractUpdateForm
  },
  data() {
    return {
      isProcessing: false,
      isLoading: false,
      title: "",
      status: "",
      items: []
    }
  },
  methods: {
    fetchContract(id) {
      var url = apiUrl + "/salesorder/" + id;
      axios
        .get(url)
        .then(r => r.data)
        .then(res => {
          this.items = res.data;
          // this.message = res.message;
        })
        .catch(err => {
          console.log(err.message)
        })
        .finally(() => {
          this.isLoading = true;
        });
    },
    // submitForm() {
    //   this.$refs.updateForm.onValitadeFormSubmit();
    // },
    vehicleUpdate(status) {
      // this.isProcessing = true
      // this.status = status;
      // // this.status = "fail";
      // setTimeout(() => {
      //   this.isProcessing = false
      //   this.status = "default";
      // }, 3000)
    }
  },
  mounted() {
    this.fetchContract(this.$route.params.id);
  }
}
</script>