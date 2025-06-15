<template>
    <div>
        <div class = "mx-20 my-4" v-if="!action.get.loading">
            <h1 class="font-black text-5xl text-gray-900">{{ action.doc.title }}</h1>
            <Button @click="router.back()">Go Back</Button>
        </div>
        <LoadingIndicator class="w-6 text-center text-blue-500" v-else/>
    </div>
</template>

<script lang="js" setup>
// @ts-nocheck
import { useRouter } from 'vue-router';
import { Button, createDocumentResource, LoadingIndicator } from 'frappe-ui';

const router = useRouter();
const props = defineProps(['name'])

const action = createDocumentResource({
    doctype: 'Action',
    name: props.name,
});
</script>