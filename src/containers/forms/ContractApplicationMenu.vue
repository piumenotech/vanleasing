<template>
   <application-menu>
      <vue-perfect-scrollbar :settings="{ suppressScrollX: true, wheelPropagation: false }">
         <div class="p-4">
            <p class="text-muted text-small mb-3">{{ $t('pages.last-update') }}</p>
            <p>
               <i class="simple-icon-clock" />
               <span class="d-inline-block ml-2">{{ items.updated_at | datetime }}</span>
            </p>
            <p class="text-muted text-small mb-1">{{ $t('contract.assign-to') }}</p>
         </div>
      </vue-perfect-scrollbar>
   </application-menu>
</template>

<script>
import moment from 'moment';
import { apiUrl } from "../../constants/config";
import ApplicationMenu from "../../components/Common/ApplicationMenu";
export default {
   components: {
      "application-menu": ApplicationMenu
   },
   props: ["items"],
   filters: {
      datetime: function (date) {
         return moment(date).calendar();
         // return moment(date).startOf('day').fromNow()
      }
   },
   data() {
      return {
         selectedVehicle: []
      }
   },
   methods: {
      getVehicle(id) {
         let url = apiUrl + "/purchaseorder/" + id;
         fetch(
           url
         ).then(res => {
           res.json().then(json => (this.selectedVehicle = json.data));
           loading(false);
         });
      }
   },
   mounted() {
      document.body.classList.add("right-menu");
      this.getVehicle(this.items.id_purchase_order);
   },
   beforeDestroy() {
      document.body.classList.remove("right-menu");
   }
};
</script>
