<template>
  <b-modal
    id="modalright"
    ref="modalright"
    :title="$t('contract.add-new')"
    modal-class="modal-right"
  >
    <div v-if="isProcessing" class="bg-white pr-5 w-100 h-100 d-flex justify-content-center align-items-center position-absolute opacity-50 z-index-10">
      <b-spinner variant="black" label="Spinning" class="text-center"></b-spinner>
    </div>
   
    <b-form
      id="addContractForm"
      class="av-tooltip tooltip-right-bottom"
      label="Bottom Right"
      @submit.prevent="onAddContractSubmit"
      >
      <b-form-group :label="$t('contract.vehicle-registration')" class="has-float-label">
        <v-select
          label="id"
          v-model="$v.contractForm.vehicleRegistration.$model"
          :filterable="false"
          :options="dynamicDataOptions"
          @search="fetchOptions"
          :dir="direction"
        >
          <template slot="no-options">type to search Vehicle registration..</template>
          <template slot="option" slot-scope="option">
            <div class="d-center">
              {{ option.vehicle_registration }}
            </div>
          </template>
          <template slot="selected-option" slot-scope="option">
            <div class="selected d-center">
              {{ option.vehicle_registration }}
            </div>
          </template>
          <template slot="spinner" slot-scope="spinner">
            <div class="spinner-border text-primary" v-show="spinner"></div>
          </template>
        </v-select>
        <div v-if="!$v.contractForm.vehicleRegistration.required"
          :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.vehicleRegistration.$error && !$v.contractForm.vehicleRegistration.required }"
        >This field is required</div>
      </b-form-group>
      <b-form-group :label="$t('contract.customer-name')" class="has-float-label">
        <b-form-input
          type="text"
          v-model.trim="$v.contractForm.customerName.$model"
          :state="!$v.contractForm.customerName.$error"
        />
        <b-form-invalid-feedback v-if="!$v.contractForm.customerName.required">Please enter a customer name</b-form-invalid-feedback>
      </b-form-group>
      <b-form-group :label="$t('contract.type')" class="has-float-label">
        <v-select v-model="$v.contractForm.contractType.$model" :options="selectData"
        :dir="direction" />
        <div :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.contractType.$error && !$v.contractForm.contractType.required }"
        >Please select at least one type</div>
      </b-form-group>
      <b-form-group :label="$t('contract.agreement')" class="has-float-label">
        <b-form-input
          type="text"
          v-model.trim="$v.contractForm.agreementNumber.$model"
          v-mask="'####'"
          :state="!$v.contractForm.agreementNumber.$error"
        />
        <b-form-invalid-feedback v-if="!$v.contractForm.agreementNumber.required">Please enter agreement no</b-form-invalid-feedback>
        <b-form-invalid-feedback v-else-if="!$v.contractForm.agreementNumber.numeric">Must be a number</b-form-invalid-feedback>
        <b-form-invalid-feedback v-else-if="!$v.contractForm.agreementNumber.minLength">Minimum 4 characters</b-form-invalid-feedback>
      </b-form-group>
      <b-form-group :label="$t('contract.period')" class="has-float-label">
        <b-form-input
        v-model.trim="$v.contractForm.contractPeriod.$model"
        :state="!$v.contractForm.contractPeriod.$error" />
        <b-form-invalid-feedback v-if="!$v.contractForm.contractPeriod.required">Contract period is required!</b-form-invalid-feedback>
        <b-form-invalid-feedback v-if="!$v.contractForm.contractPeriod.numeric">Must be number!</b-form-invalid-feedback>
      </b-form-group>
      <div class="form-group has-float-label">
        <datepicker
          :bootstrap-styling="true"
          v-model="$v.contractForm.startDate.$model"
        ></datepicker>
        <span>{{ $t('contract.start-date') }}</span>
        <div
          :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.startDate.$error && !$v.contractForm.startDate.required }"
        >Please select a date</div>
      </div>
      <b-form-row>
        <b-colxx sm="6">
          <b-form-group :label="$t('contract.mileage')">
            <b-input-group>
              <b-form-input
                type="text"
                v-model.trim="$v.contractForm.annualMileage.$model"
                :state="!$v.contractForm.annualMileage.$error"
              />
            </b-input-group>
            <div v-if="!$v.contractForm.annualMileage.required"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.annualMileage.$error && !$v.contractForm.annualMileage.required }"
            >This field is required!</div>
            <div v-else-if="!$v.contractForm.annualMileage.numeric"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.annualMileage.$error && !$v.contractForm.annualMileage.numeric }"
            >Must be a number</div>
          </b-form-group>
        </b-colxx>
        <b-colxx sm="6">
          <b-form-group :label="$t('contract.doc-fee')">
            <b-input-group>
              <money v-model="$v.contractForm.docFee.$model" v-bind="money" class="form-control" :state="!$v.contractForm.docFee.$error"></money>
            </b-input-group>
            <div v-if="!$v.contractForm.docFee.required"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.docFee.$error && !$v.contractForm.docFee.required }"
            >This field is required!</div>
          </b-form-group>
        </b-colxx>
      </b-form-row>
      <b-form-row>
        <b-colxx sm="6">
          <b-form-group :label="$t('contract.initial-rental')">
            <b-input-group>
              <money v-model="$v.contractForm.initialRental.$model" v-bind="money" class="form-control" :state="!$v.contractForm.initialRental.$error"></money>
            </b-input-group>
            <div v-if="!$v.contractForm.initialRental.required"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.initialRental.$error && !$v.contractForm.initialRental.required }"
            >This field is required!</div>
          </b-form-group>
        </b-colxx>
        <b-colxx sm="6">
          <b-form-group :label="$t('contract.monthly-rental')">
            <b-input-group>
              <money v-model="$v.contractForm.monthlyRental.$model" v-bind="money" class="form-control" :state="!$v.contractForm.monthlyRental.$error"></money>
            </b-input-group>
            <div v-if="!$v.contractForm.monthlyRental.required"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.monthlyRental.$error && !$v.contractForm.monthlyRental.required }"
            >This field is required!</div>
          </b-form-group>
        </b-colxx>
      </b-form-row>
      <b-form-row>
        <b-colxx sm="6">
          <b-form-group :label="$t('contract.delivery-charge')">
            <b-input-group>
              <money v-model="$v.contractForm.otherIncome.$model" v-bind="money" class="form-control" :state="!$v.contractForm.otherIncome.$error"></money>
            </b-input-group>
            <div v-if="!$v.contractForm.otherIncome.required"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.otherIncome.$error && !$v.contractForm.otherIncome.required }"
            >This field is required!</div>
          </b-form-group>
        </b-colxx>
        <b-colxx sm="6">
          <b-form-group :label="$t('contract.residual-value')">
            <b-input-group>
              <money v-model="$v.contractForm.residualValue.$model" v-bind="money" class="form-control"  :state="!$v.contractForm.residualValue.$error"></money>
            </b-input-group>
            <div v-if="!$v.contractForm.residualValue.required"
              :class="{ 'invalid-feedback': true, 'd-block': $v.contractForm.residualValue.$error && !$v.contractForm.residualValue.required }"
            >This field is required!</div>
          </b-form-group>
        </b-colxx>
      </b-form-row>
    </b-form>

    <template slot="modal-footer">
      <b-button variant="outline-secondary" @click="hideModal('modalright')">{{ $t('pages.cancel') }}</b-button>
      <b-button
        :class="{
          'btn-multiple-state': true,
          'show-spinner': status === 'processing',
          'show-success': status === 'success',
          'show-fail': status === 'fail'
        }"
        variant="primary" type="submit" form="addContractForm"
        :disabled="status != 'default' || this.$v.$anyError">
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
        <span v-if="buttonTitle" class="label">
          {{ buttonTitle }}
        </span>
        <span v-else class="label">
          {{ $t('pages.submit') }}
        </span>
      </b-button>
    </template>
  </b-modal>
</template>
<script>
import axios from 'axios'
import { apiUrl } from "../../constants/config";
import { Money } from 'v-money';
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";
import { getDirection } from "../../utils";
import Datepicker from "vuejs-datepicker";
import {
  validationMixin
} from "vuelidate";
const {
  required,
  numeric,
  minLength,
  helpers
} = require("vuelidate/lib/validators");

export default {
  components: {
    "v-select": vSelect,
    "datepicker": Datepicker,
    "money": Money
  },
  props: ["categories", "statuses"],
  data() {
    return {
      buttonTitle: "",
      isProcessing: false,
      status: "default",
      message: "",
      dynamicDataOptions: [],
      contractForm: {
        vehicleRegistration: null,
        customerName: "",
        agreementNumber: "",
        contractType: null,
        contractPeriod: "",
        startDate: null,
        annualMileage: "",
        docFee: "",
        initialRental: "",
        monthlyRental: "",
        otherIncome: "",
        residualValue: ""
      },
      direction: getDirection().direction,
      selectData: [
        "Contract Hire (Unregulated)",
        "Hire (Unregulated)"
      ],
      money: {
        decimal: ',',
        thousands: '.',
        prefix: '£ ',
        precision: 2,
        masked: false /* doesn't work with directive */
      }
    };
  },
  mixins: [validationMixin],
  validations: {
    contractForm: {
      vehicleRegistration: { required },
      customerName: { required },
      agreementNumber: {
        required,
        numeric,
        minLength: minLength(4)
      },
      contractType: { required },
      contractPeriod: { required, numeric },
      startDate: { required },
      annualMileage: { required, numeric },
      docFee: { required },
      initialRental: { required },
      monthlyRental: { required },
      otherIncome: { required },
      residualValue: { required }
    }
  },
  methods: {
    formatDate(date) {
      // let newDate = new Date(date);
      // return moment(newDate).format("Y-MM-DD");
      return new Date(date).toISOString().substr(0, 10)
    },
    fetchOptions(search, loading) {
      let url = apiUrl + "/purchaseorderall?search=" + encodeURI(search);
      loading(true);
      setTimeout(() => {
        fetch(
          url
        ).then(res => {
          res.json().then(json => (this.dynamicDataOptions = json.data.data));
          loading(false);
        });
      }, 1000);
    },
    onAddContractSubmit() {
      let url = apiUrl + "/salesorder";
      this.$v.$touch();
      const newContract = {
        id_purchase_order: (this.contractForm.vehicleRegistration) ? this.contractForm.vehicleRegistration.id : null,
        cust_name: this.contractForm.customerName,
        agreement_number: this.contractForm.agreementNumber,
        type: this.contractForm.contractType,
        term_months: this.contractForm.contractPeriod,
        contract_start_date: this.formatDate(this.contractForm.startDate),
        annual_mileage: this.contractForm.annualMileage,
        documentation_fees: this.contractForm.docFee,
        initial_rental: this.contractForm.initialRental,
        monthly_rental: this.contractForm.monthlyRental,
        other_income: this.contractForm.otherIncome,
        residual_value: this.contractForm.residualValue
      }
      // console.log("adding item : ", newContract);
      this.isProcessing = true;
      this.status = "processing";
      axios
        .post(url, newContract)
        .then(r => r.data)
        .then(res => {
          this.isProcessing = false;
          this.status = "success";
          this.message = "Your data was saved!";
          setTimeout(() => {
            this.$emit('added-data-table');
          }, 2000)
        }).catch(_error => {
          this.status = "fail";
          this.message = "An error occured while saving the data. Please try again later.";
          setTimeout(() => {
            this.isProcessing = false;
            this.status = "default";
            this.buttonTitle = "Try again"
          }, 1000)
        })
    },
    hideModal(refname) {
      this.status = "default";
      this.$refs[refname].hide();
    }
  }
};
</script>

