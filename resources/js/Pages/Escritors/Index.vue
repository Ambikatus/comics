<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, useForm } from '@inertiajs/vue3';
import DangerButton from '@/Components/DangerButton.vue';
import InputError from '@/Components/InputError.vue';
import Modal from '@/Components/Modal.vue';
import SecondaryButton from '@/Components/SecondaryButton.vue';
import WarningButton from '@/Components/WarningButton.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import DarkButton from '@/Components/DarkButton.vue';
import InputGroup from '@/Components/InputGroup.vue';
import { ref } from 'vue';

const props = defineProps({
    escritors:{type:Object}
});
const form = useForm({name:'',last_name:''});
const v = ref({id:'',name:'',last_name:'',comics:[]});
const showModalView = ref(false);
const showModalForm = ref(false);
const showModalDel = ref(false);
const title = ref('');
const operation = ref(1);
const msj = ref('');
const classMsj = ref('hidden');

const openModalView = (a) => {
    v.value.name = a.last_name+' '+a.name;
    v.value.comics = a.comics;
    showModalView.value = true; 
}
const openModalForm = (op,a) => {
    showModalForm.value = true;
    operation.value = op;
    if (op == 1) {
        title.value = 'Crear escritor';
    }
    else{
        title.value = 'Editat escritor';
        form.name = a.name;
        form.last_name = a.last_name;
        v.value.id = a.id;
    }
}
const openModalDel = (a) => {
    showModalDel.value = true;
    v.value.id = a.id;
    v.value.name = a.last_name+' '+a.name;
}
const closeModalView = () => {
    showModalView.value = false;
}
const closeModalForm = () => {
    showModalForm.value = false;
    form.reset();
}
const closeModalDel = () => {
    showModalDel.value = false;
}

const save = () => {
    if (operation.value == 1) {
        form.post(route('escritors.store'),{
            onSuccess: () => {ok('Escritor creado')}
        });
    }
    else{
        form.put(route('escritors.update',v.value.id),{
            onSuccess: () => {ok('Escritor actualizado')}
        });
    }
}
const ok = (m) => {
    if (operation.value == 2) {
        closeModalForm();
    }
    closeModalDel();
    form.reset();
    msj.value = m;
    classMsj.value = 'block'
    setTimeout(() => {
        classMsj.value = 'hidden';
    }, 8000);
}

const deleteEscritor = () =>{
    form.delete(route('escritors.destroy',v.value.id),{
        onSuccess: () => { ok('Escritor eliminado')}
    })
}

</script>

<template>
    <Head title="Escritores" />
    <AuthenticatedLayout>
		<template #header>
			Escritores
            <DarkButton @click="openModalForm(1)">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M18 7.5v3m0 0v3m0-3h3m-3 0h-3m-2.25-4.125a3.375 3.375 0 1 1-6.75 0 3.375 3.375 0 0 1 6.75 0ZM3 19.235v-.11a6.375 6.375 0 0 1 12.75 0v.109A12.318 12.318 0 0 1 9.374 21c-2.331 0-4.512-.645-6.374-1.766Z" />
                </svg>

            </DarkButton>
		</template>
		
		<div class="w-full overflow-hidden rounded-lg border shadow-md">
            <div class="inline-flex overflow-hidden mb-4 w-full bg-white rounded-lg shadow-md" :class="classMsj">
				<div class="flex justify-center items-center w-12 bg-green-500">
					<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                         <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" />
                    </svg>

				</div>
				
				<div class="px-4 py-2 -mx-3">
					<div class="mx-3">
						<span class="font-semibold text-green-500">Success</span>
						<p class="text-sm text-gray-600">{{ msj }}</p>
					</div>
				</div>
			</div>
			<div class="w-full overflow-x-auto bg-white">
                <table class="w-full whitespace-no-wrap">
                    <thead>
                        <tr class="text-xs font-semibold tracking-wide text-left text-gray-500 uppercase border-b dark:border-gray-700 bg-gray-50 dark:text-gray-400 dark:bg-gray-800">
                      <th class="px-4 py-3">#header</th>
                      <th class="px-4 py-3">Name</th>
                      <th class="px-4 py-3">Last Name</th>
                      <th class="px-4 py-3">Detail</th>
                      <th class="px-4 py-3">Edit</th>
                      <th class="px-4 py-3">Delete</th>
                    </tr>
                    </thead>
                    <tbody class="bg-white divide-y dark:divide-gray-700 dark:bg-gray-800">
                    <tr v-for="a,i in escritors" :key="a.id" class="text-gray-700">
                        <td class="px-4 py-3 text-sm">
                            {{ (i+1) }}
                        </td>
                        <td class="px-4 py-3 text-sm">
                            {{ a.name }}
                        </td>
                        <td class="px-4 py-3 text-sm">
                            {{ a.last_name }}
                        </td>
                        <td class="px-4 py-3 text-sm">
                            <SecondaryButton @click="openModalView(a)">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z" />
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
                                </svg>

                            </SecondaryButton>
                        </td>
                        <td class="px-4 py-3 text-sm">
                            <WarningButton @click="openModalForm(2,a)">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L6.832 19.82a4.5 4.5 0 0 1-1.897 1.13l-2.685.8.8-2.685a4.5 4.5 0 0 1 1.13-1.897L16.863 4.487Zm0 0L19.5 7.125" />
                                </svg>

                            </WarningButton>
                        </td>
                        <td class="px-4 py-3 text-sm">
                            <DangerButton @click="openModalDel(a)">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0" />
                                </svg>
                            </DangerButton>
                        </td>
                    </tr>
                  </tbody>
                </table>
            </div>
		</div>
        <Modal :show="showModalView" @close="closeModalView">
            <div class="p-6">
                <p>Escritor: <span class="text-lg font-medium text-gray-900">{{ v.name }}</span></p>
                Comics:
                <ol>
                    <li class="text-lg font-medium text-gray-900" v-for="b,i in v.comics">
                        {{ (i+1)+') '+b.title }}
                    </li>
                </ol>
            </div>
            <div class="m-6 flex justify-end">
                <SecondaryButton @click="closeModalView">Cancelar</SecondaryButton>
            </div>
        </Modal>
        <Modal :show="showModalForm" @close="closeModalForm">
            <div class="p-6">
                <h2 class="text-lg font-medium text-gray-900">{{ title }}</h2>
                <div class="mt-6 mb-6 space-y-6 max-w-xl">
                    <InputGroup :text="'Name'" :required="'required'" v-model="form.name" :type="'text'">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                         <path stroke-linecap="round" stroke-linejoin="round" d="M17.982 18.725A7.488 7.488 0 0 0 12 15.75a7.488 7.488 0 0 0-5.982 2.975m11.963 0a9 9 0 1 0-11.963 0m11.963 0A8.966 8.966 0 0 1 12 21a8.966 8.966 0 0 1-5.982-2.275M15 9.75a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
                        </svg>
                    </InputGroup>
                    <InputError class="mt-1" :message="form.errors.name"></InputError>
                    <InputGroup :text="'Last name'" :required="'required'" v-model="form.last_name" :type="'text'">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                         <path stroke-linecap="round" stroke-linejoin="round" d="M17.982 18.725A7.488 7.488 0 0 0 12 15.75a7.488 7.488 0 0 0-5.982 2.975m11.963 0a9 9 0 1 0-11.963 0m11.963 0A8.966 8.966 0 0 1 12 21a8.966 8.966 0 0 1-5.982-2.275M15 9.75a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
                        </svg>
                    </InputGroup>
                    <InputError class="mt-1" :message="form.errors.last_name"></InputError>
                    <PrimaryButton @click="save">Guardar</PrimaryButton>
                </div>
            </div>
            <div class="m-6 flex justify-end">
                <SecondaryButton @click="closeModalForm">Cancelar</SecondaryButton>
            </div>
        </Modal>
        <Modal :show="showModalDel" @close="closeModalDel">
            <div class="p-6">
                <p class="text-2xl text-gray-500">
                    Estas seguro de eliminar el escritor <span class="text-2xl font-medium text-gray-900">{{ v.name }}</span>?</p>
                    <PrimaryButton @click="deleteEscritor">Si, borrar</PrimaryButton>
            </div>
            <div class="m-6 flex justify-end">
                <SecondaryButton @click="closeModalDel">Cancelar</SecondaryButton>
            </div>
        </Modal>
	</AuthenticatedLayout>
</template>