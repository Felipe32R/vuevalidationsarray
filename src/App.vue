<template>
  <div class="flex flex-col h-full p-20 gap-4 px-[400px]">
    <button @click="addContact()" class="bg-gray-300">add contact</button>
    <div v-for="(contact, index) in fields" :key="index">
      <TextInput
        :label="'Name'"
        :required="true"
        v-model:model-value="contact.value.name"
        class="w-full"
        :max-length="50"
        :errors="getError(index, 'name')"
      />
      <TextInput
        :label="'E-mail'"
        :required="true"
        v-model:model-value="contact.value.email"
        class="w-full"
        :max-length="50"
        :errors="getError(index, 'email')"
      />
      <button @click="removeContact(index)" class="bg-gray-300">
        remove contact
      </button>
    </div>
    <button class="bg-green-500" @click="handleSubmit">Submit</button>
  </div>
</template>

<script setup lang="ts">
import { toTypedSchema } from "@vee-validate/zod";
import { useFieldArray, useForm } from "vee-validate";
import { z } from "zod";
import TextInput from "./components/TextInput.vue";

const addContact = () => {
  push({
    name: "",
    email: "",
  });
};

const removeContact = (index: number) => {
  remove(index);
};

type Contact = {
  name: string;
  email: string;
};

const contact = z.object({
  name: z.string({ coerce: true }).min(3),
  email: z.string({ coerce: true }).email(),
});

const validationSchema = toTypedSchema(
  z.object({
    contacts: z.array(contact).default([]),
  })
);

function getError(index: number, field: string) {
  const errorKey = `contacts[${index}].${field}`;
  if (errors.value[errorKey]) {
    return [errors.value[errorKey]];
  }
}

const { values, errors, validate } = useForm({
  validationSchema,
});

const { fields, push, insert, remove } = useFieldArray<Contact>("contacts");

async function handleSubmit() {
  console.log("errors", errors.value);
  console.log("values", values.contacts);
  console.log("fields", fields.value);

  const { valid } = await validate();

  console.log("valid", valid);
}
</script>
