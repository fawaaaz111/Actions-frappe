<template>
  <div class="max-w-3xl py-12 mx-auto">
    
    <div class="flex flex-row items-center justify-between">
      <h2 class="text-2xl font-bold mb-4">Lists</h2>
      <Button class="btn" icon-left="plus">New List</Button>
    </div>
    
    <div class="mt-3">
      <Card title="General">
        <ul>
           <li v-for="action in actions.data" 
           :key="action.name" class="flex flex-row items-center justify-between space-y-2">
             <router-link :to="`/actions/${action.name}`" class="text-blue">
                {{ action.title }}
             </router-link>
             <Button icon="check"  @click="CompleteAction(action.name)"></Button>
           </li>
          </ul>

          <Button icon-left="plus" @click="addActionDialogShown = true">New Action</Button>
      </Card>
    </div>

    <Dialog 
      :options="{
          title: 'Add New Ation',
          actions: [
            {
              label: 'Add',
              variant: 'solid',
              onClick: () => {
                AddAction();
                addActionDialogShown = false;
              },
            },
            {'label': 'Cancel', appearance: 'secondary', }
          ],
        }" 
      v-model="addActionDialogShown" 
    >
        <template #body-content>
          <div class="space-y-2">
            <Input v-model="action.title" type="text" required label="Title" placeholder="Give a title for your action ..."/>
            <Input v-model="action.category" type="select" required label="List" :options="categoryOptions"/>
          </div>
        </template>
    </Dialog>
        
  </div>
</template>

<script lang="js" setup>
// @ts-nocheck
import { Dialog, createListResource, Card, Input } from 'frappe-ui';
import { computed, reactive, ref } from 'vue';

const action = reactive({
  title: '',
  category: 'General',
});

const addActionDialogShown = ref(false);

const actions = createListResource({
  doctype: 'Action',
  fields: ['name', 'title', 'description', 'status', 'date', 'due_date'],
  limit: 10,
  filters: {
    status: 'To-do',
  },
});

actions.reload();

const categories = createListResource({
  doctype: 'Category',
  fields: ['name'],
  transform(categories) {
    return categories.map(category => category.name);
  },
  cache: "actions"
});

categories.reload();

const categoryOptions = computed(() => {
  if (categories.list.loading || !categories.data) return []

  console.log('Categories:', categories.data);
  return categories.data
});

const CompleteAction = (name) => {
  actions.setValue.submit({
    name: name,
    status: 'Completed',
    onSuccess: () => {
      actions.reload();
    },
    onError: (error) => {
      frappe.msgprint({
        title: 'Error',
        message: error.message,
        indicator: 'red',
      });
    },
  });  
};

const AddAction = () => {
  console.log('Adding action:', action);
  actions.insert.submit(action);
};

</script>
