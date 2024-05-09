<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import { onMounted } from 'vue';
import { usePage } from '@inertiajs/vue3';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Swal from 'sweetalert2/dist/sweetalert2.js'
import 'sweetalert2/src/sweetalert2.scss'
import axios from 'axios';
import { toastMixin } from '@/utils/toast';

const page = usePage();

const empty = page.props.equivalencias.length === 0;

const confirmDelete = async (id) => {
    const result = await Swal.fire({
        title: "Tem certeza que deseja excluir essa equivalencia?",
        text: "Ao excluir essa equivalencia, todas as informações relacionadas a ela também serão excluidas!",
        showCancelButton: true,
        confirmButtonText: "Sim",
        cancelButtonText: 'Não',
    });

    if (result.isConfirmed) {
        try {
            const response = await axios.delete(route('equivalencias.destroy', id));
            toastMixin.fire({
                title: response.data.success || response.data.error,
            }).then(() => { location.reload(); });
        } catch (error) {
            Swal.fire("Erro ao excluir equivalencia", "", "error");
        }
    }
}
</script>
<template>

    <Head title="Disciplinas" />

    <AuthenticatedLayout>
        <template #header>
            <div class="flex justify-between">
                <h2 class="font-semibold text-xl text-gray-800 dark:text-gray-200 leading-tight">Equivalências</h2>
                <a :href="route('equivalencias.create')"
                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full">+ Criar
                    Equivalência</a>
            </div>
        </template>
        <div class="py-12">
            <div class="max-w-2xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white dark:bg-gray-800 overflow-hidden shadow-sm sm:rounded-lg">
                    <div v-if="empty">
                        <div class="flex justify-center flex-col gap-2 items-center h-60">
                            <h2 class="text-3xl text-gray-400">Nenhuma equivalência encontrada</h2>
                        </div>
                    </div>
                    <div v-if="!empty">
                        <DataTable stripedRows paginator :rows="25" :rowsPerPageOptions="[25, 50, 100]"
                            :value="$page.props.equivalencias" tableStyle="min-width: 20rem">
                            <Column field="titulo" header="Titulo" sortable></Column>
                            <Column field="disciplinas" header="disciplinas" sortable>
                                <template #body="slotProps">
                                    <!-- {{ slotProps.data.length }} -->
                                    TODO
                                </template>
                            </Column>
                            <Column field="id" header="Açoes">
                                <template #body="slotProps">
                                    <div class="flex gap-4">
                                        <a :href="route('equivalencias.edit', slotProps.data.id)"><i
                                                class="edit-icon pi pi-pencil"></i></a>
                                        <button @click="confirmDelete(slotProps.data.id)"><i
                                                class="delete-icon pi pi-trash"></i></button>
                                    </div>
                                </template>
                            </Column>
                        </DataTable>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>