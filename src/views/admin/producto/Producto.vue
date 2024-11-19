<template>
    <Toolbar class="mb-6">
        <template #start>
            <Button label="Nuevo" icon="pi pi-plus" class="mr-2" @click="openNew" />
        </template>

        <template #end>
            <FileUpload mode="basic" accept="image/*" :maxFileSize="1000000" label="Importar" customUpload chooseLabel="Importar" class="mr-2" auto :chooseButtonProps="{ severity: 'secondary' }" />
            <Button label="Exportar" icon="pi pi-upload" severity="secondary" @click="exportCSV($event)" />
        </template>
    </Toolbar>

    <DataTable
    ref="dt"
    :value="products"
    dataKey="id"
    lazy
    :totalRecords="totalRecords"
    :loading="loading"
    :paginator="true"
    :rows="10"
    @page="onPage($event)"
    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
    :rowsPerPageOptions="[3, 10, 25]"
    currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} productos"
>
    <template #header>
        <div class="flex flex-wrap gap-2 items-center justify-between">
            <h4 class="m-0">Gestión Productos</h4>
            <IconField>
                <InputIcon>
                    <i class="pi pi-search" />
                </InputIcon>
                <InputText  placeholder="Buscar..." v-model="buscar" @change="getProductos()" />
            </IconField>
        </div>
    </template>

    <Column field="id" header="ID" sortable style="min-width: 3rem"></Column>
    <Column field="nombre" header="NOMBRE" sortable style="min-width: 16rem"></Column>
    <Column header="Image">
        <template #body="slotProps">
            <img :src="`https://primefaces.org/cdn/primevue/images/product/${slotProps.data.image}`" :alt="slotProps.data.image" class="rounded" style="width: 64px" />
        </template>
    </Column>
    <Column field="precio" header="PRECIO" sortable style="min-width: 5rem">
        <template #body="slotProps">
            {{ formatCurrency(slotProps.data.precio) }}
        </template>
    </Column>
    <Column field="categoria_id" header="Categoria" sortable style="min-width: 6rem"></Column>
  
    <Column :exportable="false" style="min-width: 12rem">
        <template #body="slotProps">
            <Button icon="pi pi-pencil" outlined rounded class="mr-2" @click="editProduct(slotProps.data)" />
            <Button icon="pi pi-trash" outlined rounded severity="danger" @click="confirmDeleteProduct(slotProps.data)" />
        </template>
    </Column>
</DataTable>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import productoService from "./../../../services/producto.service"


const products = ref([]);
const totalRecords = ref(0)
const loading = ref(false)
const lazyParams = ref({});
const buscar = ref("")

onMounted(() => {
    loading.value = true;

lazyParams.value = {
    first: 0,
    rows: 10,
    sortField: null,
    sortOrder: null,
};

    getProductos()
})


const formatCurrency = (value) => {
    if(value)
        return value.toLocaleString('en-US', {style: 'currency', currency: 'USD'});
    return;
};

const getProductos = async () =>{
    loading.value = true;
    console.log(lazyParams.value);
    const {data} = await productoService.listar(lazyParams.value.page+1, lazyParams.value.rows, buscar.value);

    products.value = data.data;
    totalRecords.value = data.total;

    loading.value = false
}

const onPage = (event) => {
    lazyParams.value = event;
    getProductos(event);
};

</script>