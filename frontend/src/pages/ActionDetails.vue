<template>
    <div class="max-w-6xl py-12 px-5 mx-auto">
        <div class = "mx-20 my-4" v-if="!action.get.loading">
            <div class="flex flex-row items-center justify-between">
                <div class="flex items-center space-x-4">
                    <h1 class="font-black text-5xl text-gray-900">{{ action.doc.title }}</h1>
                    <Badge 
                        v-if="action.doc.category" 
                        :label="action.doc.category"
                        variant="subtle"
                        size="md"
                        class="mt-2"
                    />
                </div>
                
                <div class="flex flex-row items-center space-x-2">
                    <Button icon-left="arrow-left" @click="router.back()">Go Back</Button>
                    <Button 
                        @click="action.setValue.submit({ status: 'Archived' })"
                        :disabled="action.doc.status === 'Archived'"
                        icon-left="trash" appearance="white"
                        class="text-red-400 border-red-400"
                    >
                        Delete
                    </Button>
                    <Button
                        @click="action.setValue.submit({ status: 'Completed' })"
                        :disabled="action.doc.status === 'Completed'"
                        icon-left="check" appearance="solid"
                        class="text-green-500 border-green-500"
                    >
                        Mark As Done
                    </Button>
                </div>
            </div>

            <hr class="my-2 pb-3" />

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-20 mt-6">
                <div class="flex flex-col space-y-4">
                    <h3 class="font-bold text-lg text-gray-900">Dates</h3>
                    <div class="flex flex-col sm:flex-row items-start sm:items-center space-y-2 sm:space-y-0 sm:space-x-3">
                        <div class="flex flex-col space-y-1">
                            <label class="text-sm font-medium text-gray-600">Start Date</label>
                            <DatePicker v-model="action.doc.date" />
                        </div>
                        <div class="flex flex-col space-y-1">
                            <label class="text-sm font-medium text-gray-600">Due Date</label>
                            <DatePicker 
                                v-model="date"
                                variant="subtle"
                                placeholder="Set Due Date"
                            />
                        </div>
                    </div>

                    <div class="pt-12 flex flex-col space-y-4" v-if="action.doc.action_task && action.doc.action_task.length > 0">
                        <h3 class="font-bold text-lg text-gray-900">Tasks</h3>
                        <div class="border rounded-lg border-gray-200 p-3">
                            <div 
                                v-for="(task, index) in action.doc.action_task" 
                                :key="task.name || index"
                                class="flex items-center p-1 border-gray-200 rounded-lg hover:bg-gray-50 transition-colors"
                            >
                                <Checkbox 
                                    :label="task.description || `Task ${index + 1}`"
                                />
                            </div>
                        </div>
                    </div>
                </div>

                <div class="flex flex-col space-y-4">
                    <h3 class="font-bold text-lg text-gray-900">Description</h3>
                    <div class="border border-gray-200 rounded-lg overflow-hidden">
                        <TextEditor
                            ref="textEditor"
                            editor-class="prose-sm min-h-[8rem] p-3 focus:outline-none"
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
            </div>
        </div>
        <LoadingIndicator class="w-6 text-center text-blue-500" v-else/>
    </div>
</template>

<script lang="js" setup>
// @ts-nocheck
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { TextEditor, Button, createDocumentResource, LoadingIndicator, DatePicker, Checkbox, Badge } from 'frappe-ui';

const router = useRouter();
const props = defineProps(['name'])
const content = ref('');

const action = createDocumentResource({
    doctype: 'Action',
    name: props.name,
    cache: "action",
    // auto: true
});
</script>