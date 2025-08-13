<template>
  <div class="max-w-3xl py-12 px-5 mx-auto">
    
    <div class="flex flex-row items-center justify-between mb-4">
      <h2 class="text-2xl font-bold">Lists</h2>
      <Button class="btn" icon-left="plus" @click="addCategoryDialogShown = true">New List</Button>
    </div>
    
    <div class="mt-3">
      <!-- Loading state -->
      <div v-if="categories.list.loading" class="text-center py-8">
        <p>Loading categories...</p>
      </div>
      
      <!-- No categories state -->
      <div v-else-if="!categories.data || categories.data.length === 0" class="text-center py-8">
        <p class="text-gray-500 mb-4">No categories found. Create your first category!</p>
        <Button icon-left="plus" @click="addCategoryDialogShown = true">Create Category</Button>
      </div>
      
      <!-- Grid container for cards -->
      <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <!-- Card for each category -->
        <Card v-for="category in categories.data" :key="category" :title="category">
          <ul>
            <li v-for="action in getActionsByCategory(category)" 
            :key="action.name" class="flex flex-row items-center justify-between space-y-2">
              <router-link :to="`/actions/${action.name}`" class="text-blue">
                {{ action.title }}
              </router-link>
              <Button icon="check" @click="CompleteAction(action.name)"></Button>
            </li>
          </ul>

          <div class="flex justify-center mt-4">
            <Button icon-left="plus" @click="openAddActionDialog(category)">New Action</Button>
          </div>
        </Card>
      </div>
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

    <!-- New Category Dialog -->
    <Dialog 
      :options="{
          title: 'Add New Category',
          actions: [
            {
              label: 'Add',
              variant: 'solid',
              onClick: () => {
                AddCategory();
                addCategoryDialogShown = false;
              },
            },
            {'label': 'Cancel', appearance: 'secondary', }
          ],
        }" 
      v-model="addCategoryDialogShown" 
    >
        <template #body-content>
          <div class="space-y-2">
            <Input v-model="newCategory.title" type="text" required label="Category Name" placeholder="Enter category name (e.g., Work, Personal, Shopping)"/>
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

const newCategory = reactive({
  title: '',
});

const addActionDialogShown = ref(false);
const addCategoryDialogShown = ref(false);

const actions = createListResource({
  doctype: 'Action',
  fields: ['name', 'title', 'description', 'status', 'date', 'due_date', 'category'],
  limit: 10,
  filters: {
    status: 'To-do',
  },
});

actions.reload();

const categories = createListResource({
  doctype: 'Category',
  fields: ['name', 'title'],
  transform(categories) {
    return categories.map(category => category.title);
  },
  cache: "actions"
});

categories.reload();

const categoryOptions = computed(() => {
  if (categories.list.loading || !categories.data) return []

  return categories.data
});

// Filter actions by category
const getActionsByCategory = (category) => {
  if (!actions.data) return []
  return actions.data.filter(action => action.category === category)
}

// Open dialog with pre-selected category
const openAddActionDialog = (category) => {
  action.category = category
  addActionDialogShown.value = true
}

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
  actions.insert.submit({
    ...action,
    onSuccess: () => {
      // Reset form
      action.title = ''
      action.category = 'General'
      // Reload actions to show the new one
      actions.reload()
    },
    onError: (error) => {
      frappe.msgprint({
        title: 'Error',
        message: error.message,
        indicator: 'red',
      });
    }
  })
};

const AddCategory = () => {
  categories.insert.submit({
    ...newCategory,
    onSuccess: () => {
      // Reset form
      newCategory.title = ''
      // Reload categories to show the new one
      categories.reload()
    },
    onError: (error) => {
      frappe.msgprint({
        title: 'Error',
        message: error.message,
        indicator: 'red',
      });
    }
  })
};

</script>
