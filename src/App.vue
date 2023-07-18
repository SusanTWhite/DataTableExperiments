<template>
  <div class="card">
    <div class="flex justify-content-end">
      <span class="p-input-icon-left">
        <i class="pi pi-search" />
        <InputText
          v-model="filters['global'].value"
          placeholder="Keyword Search"
        />
      </span>
    </div>
  </div>
  <div class="card">
    <DataTable
      v-model:filters="filters"
      :value="customers"
      paginator
      :rows="5"
      dataKey="id"
      filterDisplay="row"
      :loading="loading"
      :globalFilterFields="['name', 'representative.name', 'status']"
    >
      <template #empty> No customers found. </template>
      <template #loading> Loading customers data. Please wait. </template>
      <Column field="name" header="Name" style="min-width: 12rem">
        <template #body="{ data }">
          {{ data.name }}
        </template>
      </Column>
      <Column
        header="Agent"
        filterField="representative"
        :showFilterMenu="false"
        :filterMenuStyle="{ width: '14rem' }"
        style="min-width: 14rem"
      >
        <template #body="{ data }">
          <div class="flex align-items-center gap-2">
            <span>{{ data.representative.name }}</span>
          </div>
        </template>
      </Column>
      <Column
        field="status"
        header="Status"
        :showFilterMenu="false"
        :filterMenuStyle="{ width: '14rem' }"
        style="min-width: 12rem"
      >
        <template #body="{ data }">
          <Tag :value="data.status" :severity="getSeverity(data.status)" />
        </template>
      </Column>
    </DataTable>
  </div>
  <div class="card">
    <DataTable
      v-model:filters="filters"
      :value="customers"
      paginator
      :rows="1"
      dataKey="id"
      filterDisplay="row"
      :loading="loading"
      :globalFilterFields="['name', 'country.name']"
    >
      <template #empty> No customers found. </template>
      <template #loading> Loading customers data. Please wait. </template>
      <Column field="name" header="Name" style="min-width: 12rem">
        <template #body="{ data }">
          {{ data.name }}
        </template>
      </Column>
      <Column
        header="Country"
        filterField="country.name"
        style="min-width: 12rem"
      >
        <template #body="{ data }">
          <div class="flex align-items-center gap-2">
            <span>{{ data.country.name }}</span>
          </div>
        </template>
      </Column>
    </DataTable>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { FilterMatchMode } from 'primevue/api';
import { CustomerService } from '@/service/CustomerService';

const customers = ref();
const filters = ref({
  global: { value: null, matchMode: FilterMatchMode.CONTAINS },
});
const representatives = ref([
  { name: 'Amy Elsner', image: 'amyelsner.png' },
  { name: 'Anna Fali', image: 'annafali.png' },
  { name: 'Asiya Javayant', image: 'asiyajavayant.png' },
  { name: 'Bernardo Dominic', image: 'bernardodominic.png' },
  { name: 'Elwin Sharvill', image: 'elwinsharvill.png' },
  { name: 'Ioni Bowcher', image: 'ionibowcher.png' },
  { name: 'Ivan Magalhaes', image: 'ivanmagalhaes.png' },
  { name: 'Onyama Limba', image: 'onyamalimba.png' },
  { name: 'Stephen Shaw', image: 'stephenshaw.png' },
  { name: 'XuXue Feng', image: 'xuxuefeng.png' },
]);
const statuses = ref([
  'unqualified',
  'qualified',
  'new',
  'negotiation',
  'renewal',
  'proposal',
]);
const loading = ref(true);
// v-if="data.status=='qualified'"
onMounted(() => {
  CustomerService.getCustomersMedium().then((data) => {
    customers.value = getCustomers(data);
    loading.value = false;
  });
});

const getCustomers = (data) => {
  return [...(data || [])].map((d) => {
    d.date = new Date(d.date);
    //console.log('Console log: ' + d.status);
    return d;
  });
};
const formatDate = (value) => {
  return value.toLocaleDateString('en-US', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
  });
};
const formatCurrency = (value) => {
  return value.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
};
const getSeverity = (status) => {
  switch (status) {
    case 'unqualified':
      return 'danger';

    case 'qualified':
      return 'success';

    case 'new':
      return 'info';

    case 'negotiation':
      return 'warning';

    case 'renewal':
      return null;
  }
};
</script>
