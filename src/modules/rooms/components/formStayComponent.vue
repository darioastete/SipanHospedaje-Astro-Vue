<script setup lang="ts">
import Button from "@components/ButtonComponent.vue";
import Input from "@components/InputComponent.vue";
import formClientComponent from "@maintenance/client/components/formClientComponent.vue";

import { useVuelidate } from "@vuelidate/core";
import { minLength, required } from "@vuelidate/validators";
import { ref } from "vue";

import { onMounted } from "vue";

const room_id = defineModel("room_id", { type: String, default: "" });
const id_client = defineModel("id_client", { type: String, default: "" });
const check_in_date = defineModel("check_in_date", {
	type: String,
	default: "",
});
const check_out_date = defineModel("check_out_date", {
	type: String,
	default: "",
});
const total_price = defineModel("total_price", { type: String, default: "" });
const notes = defineModel("notes", { type: String, default: "" });
const client = defineModel("client", { type: Object, default: {} });

const formClientRef = ref();

const { roomIdSel } = defineProps<{
	listTypeDoc: any[];
	roomIdSel: string;
	viewMode: boolean;
}>();

room_id.value = roomIdSel;

const form = ref({
	room_id,
	id_client,
	check_in_date,
	check_out_date,
	total_price,
	notes,
	client,
});

const disabledFormClient = ref(true);

const switchEnableFormClient = async (value: boolean) => {
	if (value) {
		disabledFormClient.value = true;
	} else {
		disabledFormClient.value = false;
	}
	form.value.id_client = "";
	formClientRef.value.clearForm();
};
const rules = {
	check_in_date: {
		required,
		$autoDirty: true,
	},
	check_out_date: {
		required,
		$autoDirty: true,
	},
	total_price: {
		required,
		$autoDirty: true,
	},
	notes: {
		minLength: minLength(3),
		$autoDirty: true,
	},
};

const clearForm = () => {
	formClientRef.value.clearForm();
	$form.value.$reset();
};

const findClient = (id: string) => {
	form.value.id_client = id;
	console.log(form.value);
};

const $form = useVuelidate(rules, form);

defineExpose({
	$form,
	clearForm,
});

onMounted(async () => {});
</script>
<template>
  <form @submit.prevent="$emit('sumbit')">
    <section class="flex flex-col mt-3 shadow-lg px-5 py-4 rounded-md">
      <div class="flex justify-between items-center">
        <h2 class="sm:text-sm text-primary-yellow-300 mb-3">Cliente</h2>
        <button
          type="button"
          class="text-primary-yellow-300 mb-3 text-xs font-semibold"
          @click="switchEnableFormClient(false)"
          v-if="disabledFormClient"
        >
          Nuevo +
        </button>
        <button
          type="button"
          class="text-primary-yellow-300 mb-3 text-xs font-semibold"
          @click="switchEnableFormClient(true)"
          v-else
        >
          Cancelar Nuevo
        </button>
      </div>
      <!-- <template> -->

      <formClientComponent
        @findClient="findClient"
        ref="formClientRef"
        :disabled="disabledFormClient"
        :create-button="false"
        :search="disabledFormClient"
        :listTypeDoc="listTypeDoc"
        v-model:name="form.client.name"
        v-model:last_name="form.client.last_name"
        v-model:id_document_type="form.client.id_document_type"
        v-model:document="form.client.document"
        v-model:cell_phone="form.client.cell_phone"
        v-model:mail="form.client.mail"
        v-model:ocupation="form.client.ocupation"
      />
      <!-- </template> -->
    </section>

    <section class="flex flex-col mt-3 shadow-lg px-5 py-4 rounded-md">
      <h2 class="sm:text-sm text-primary-yellow-300 mb-3">Datos de Reserva</h2>
      <div class="grid grid-cols-2">
        <Input
          id="RoomFormStay"
          label="Ingreso"
          type="datetime-local"
          v-model="check_in_date"
          :has-error="$form.check_in_date.$error"
        />
        <Input
          id="RoomFormStay"
          label="Salida"
          type="datetime-local"
          :has-error="$form.check_out_date.$error"
          v-model="check_out_date"
        />
        <Input
          id="RoomFormStay"
          label="Total"
          type="text"
          :has-error="$form.total_price.$error"
          v-model="total_price"
        />

        <Input
          id="RoomFormStay"
          label="Notas"
          type="text"
          :has-error="$form.notes.$error"
          v-model="notes"
        />
      </div>
    </section>
    <div class="flex justify-end gap-2 mt-4">
      <Button
        type="button"
        color="secondary"
        @click="$emit('sendCloseModal')"
        label="Cancelar"
      />
      <Button label="Guardar Cambios" />
    </div>
  </form>
</template>
