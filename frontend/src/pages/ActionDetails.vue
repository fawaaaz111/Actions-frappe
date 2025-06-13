<template>
    <div>
        <h1>action details: {{ props.name }}</h1>
        <Button @click="router.back()">Go Back</Button>
    </div>
</template>

<script lang="js" setup>
// @ts-nocheck
import { useRouter } from 'vue-router';
import { Button } from 'frappe-ui';

const router = useRouter();
const props = defineProps(['name'])
</script>