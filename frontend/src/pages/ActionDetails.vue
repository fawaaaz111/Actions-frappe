<template>
    <div>
        <div class = "mx-20 my-4" v-if="!action.get.loading">
            <div class="flex flex-row items-center justify-between">
                <h1 class="font-black text-5xl text-gray-900">{{ action.doc.title }}</h1>
                

                <div class="flex flex-row items-center space-x-2">
                    <Button icon="arrow-left" @click="router.back()">Go Back</Button>
                    <Button icon="trash" >Delete</Button>
                    <Button icon="check" >Mark As Done</Button>
                </div>
            </div>

            <div>
                <TextEditor
                    ref="textEditor"
                    editor-class="prose-sm min-h-[4rem] border rounded-b-lg border-t-0 p-2]"
                    :content="content"
                    @change="(val) => customValue = val"
                    :starterkit-options="{
                    heading: {
                        levels: [2, 3, 4],
                    },
                    }"
                    placeholder="Write something amazing..."
                />
            </div>
        </div>
        <LoadingIndicator class="w-6 text-center text-blue-500" v-else/>
    </div>
</template>

<script lang="js" setup>
// @ts-nocheck
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { TextEditor, Button, createDocumentResource, LoadingIndicator } from 'frappe-ui';

const router = useRouter();
const props = defineProps(['name'])
const content = ref('');

const action = createDocumentResource({
    doctype: 'Action',
    name: props.name,
});
</script>