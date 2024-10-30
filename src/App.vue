<template>
  <div class="flex flex-col h-full p-20 gap-4 px-[400px]">
    <button @click="addContact" class="bg-gray-300">add contact</button>
    <div v-for="(contact, index) in contactsValue">
      <TextInput
        :errors="contactsErrors"
        :label="'Name'"
        :required="true"
        v-model:model-value="contact.name"
        class="w-full"
        :max-length="50"
      />
      <TextInput
        :errors="contactsErrors"
        :label="'E-mail'"
        :required="true"
        v-model:model-value="contact.email"
        class="w-full"
        :max-length="50"
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
import { useField, useForm } from "vee-validate";
import { z } from "zod";
import TextInput from "./components/TextInput.vue";

const addContact = () => {
  contactsValue.value.push({
    name: "",
    email: "",
  });
};

const removeContact = (index: number) => {
  contactsValue.value.splice(index, 1);
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

const { values, errors, validate } = useForm({
  validationSchema,
});

const { value: contactsValue, errors: contactsErrors } =
  useField<Contact[]>("contacts");

async function handleSubmit() {
  console.log("errors", errors.value);
  console.log("contactsErrors", contactsErrors);
  await validate();
}
</script>
