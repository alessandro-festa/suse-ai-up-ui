<template>
  <div class="settings-page">
    <div class="fixed-header">
      <h1>SUSE AI Proxy Settings</h1>
      <p class="page-description">Configure the SUSE AI Universal Proxy</p>
    </div>

     <div class="tabs-container">
       <div class="tab-nav">
         <button 
           class="tab-button" 
           :class="{ active: activeTab === 'proxy' }"
           @click="activeTab = 'proxy'"
         >
           Universal Proxy
         </button>
         <button 
           v-if="wizardCompleted"
           class="tab-button" 
           :class="{ active: activeTab === 'users' }"
           @click="activeTab = 'users'"
         >
           Users
         </button>
         <button 
           v-if="wizardCompleted"
           class="tab-button" 
           :class="{ active: activeTab === 'groups' }"
           @click="activeTab = 'groups'"
         >
           Groups
         </button>
       </div>

       <div class="tab-content">
         <div v-if="activeTab === 'proxy'" class="tab-pane">
           <UniversalProxyTab />
         </div>
         
         <div v-if="activeTab === 'users' && wizardCompleted" class="tab-pane">
           <UsersTable 
             :users="users"
             :loading="usersLoading"
             :error="usersError || undefined"
             :is-external="true"
             :can-manage-users="true"
             @view-user="handleViewUser"
             @edit-user="handleEditUser"
             @add-user="handleAddUser"
             @delete-user="handleDeleteUser"
             @retry="handleRetryUsers"
           />
         </div>

         <div v-if="activeTab === 'groups' && wizardCompleted" class="tab-pane">
           <GroupsTable 
             :groups="groups"
             :loading="groupsLoading"
             :error="groupsError || undefined"
             :can-manage-groups="true"
             @view-group="handleViewGroup"
             @edit-group="handleEditGroup"
             @add-group="handleAddGroup"
             @manage-members="handleManageMembers"
             @delete-group="handleDeleteGroup"
             @retry="handleRetryGroups"
           />
         </div>
       </div>
     </div>
   </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted, watch } from 'vue'
import { useStore } from 'vuex'
import UniversalProxyTab from '../components/settings/UniversalProxyTab.vue'
import UsersTable from '../components/settings/UsersTable.vue'
import GroupsTable from '../components/settings/GroupsTable.vue'
import { useExternalUsers } from '../composables/useExternalUsers'
import { useExternalGroups } from '../composables/useExternalGroups'

export default defineComponent({
  name: 'Settings',
  components: {
    UniversalProxyTab,
    UsersTable,
    GroupsTable
  },
  setup() {
    const store = useStore()
    const activeTab = ref('proxy')
    const wizardCompleted = computed(() => store.getters['suseai/wizardCompleted'])

    // User management composables
    const { 
      users, 
      loading: usersLoading, 
      error: usersError, 
      loadUsers,
      deleteUser
    } = useExternalUsers()

    const { 
      groups, 
      loading: groupsLoading, 
      error: groupsError, 
      loadGroups,
      deleteGroup
    } = useExternalGroups()

    // Handle tab switching and data loading
    watch(activeTab, async (newTab) => {
      if (newTab === 'users' && users.value.length === 0) {
        await loadUsers()
      } else if (newTab === 'groups' && groups.value.length === 0) {
        await loadGroups()
      }
    })

    const handleRetryUsers = () => loadUsers()
    const handleRetryGroups = () => loadGroups()

    // Mock handlers for actions that aren't fully implemented yet
    const handleViewUser = (id: string) => console.log('View user:', id)
    const handleEditUser = (id: string) => console.log('Edit user:', id)
    const handleAddUser = () => console.log('Add user clicked')
    const handleDeleteUser = async (id: string) => {
      if (confirm('Are you sure you want to delete this user?')) {
        await deleteUser(id)
      }
    }

    const handleViewGroup = (id: string) => console.log('View group:', id)
    const handleEditGroup = (group: any) => console.log('Edit group:', group)
    const handleAddGroup = () => console.log('Add group clicked')
    const handleManageMembers = (group: any) => console.log('Manage members:', group)
    const handleDeleteGroup = async (id: string) => {
      if (confirm('Are you sure you want to delete this group?')) {
        await deleteGroup(id)
      }
    }

    return {
      activeTab,
      wizardCompleted,
      users,
      usersLoading,
      usersError,
      groups,
      groupsLoading,
      groupsError,
      handleRetryUsers,
      handleRetryGroups,
      handleViewUser,
      handleEditUser,
      handleAddUser,
      handleDeleteUser,
      handleViewGroup,
      handleEditGroup,
      handleAddGroup,
      handleManageMembers,
      handleDeleteGroup
    }
  }
})
</script>

<style scoped>
.settings-page {
  padding: 20px;
}

.fixed-header {
  position: sticky;
  top: 0;
  background: var(--body-bg, #fff);
  z-index: 10;
  padding-bottom: 20px;
  border-bottom: 1px solid var(--border, #dee2e6);
  margin-bottom: 24px;
}

.fixed-header h1 {
  margin: 0 0 8px 0;
  font-size: 24px;
  font-weight: 600;
  color: var(--text, #333);
}

.page-description {
  margin: 0;
  color: var(--text-muted, #666);
  font-size: 14px;
}

.tabs-container {
  background: var(--card-bg, #fff);
  border: 1px solid var(--border, #dee2e6);
  border-radius: 8px;
  overflow: hidden;
}

.tab-nav {
  display: flex;
  background: var(--nav-bg, #f8f9fa);
  border-bottom: 1px solid var(--border, #dee2e6);
}

.tab-button {
  padding: 12px 20px;
  border: none;
  background: transparent;
  color: var(--text-muted, #6c757d);
  font-size: 14px;
  font-weight: 500;
  border-bottom: 2px solid transparent;
  cursor: pointer;
  transition: all 0.2s ease;
}

.tab-button:hover {
  color: var(--primary, #007bff);
  background: var(--hover-bg, #f1f3f5);
}

.tab-button.active {
  color: var(--primary, #007bff);
  border-bottom: 2px solid var(--primary, #007bff);
  background: transparent;
}

.tab-content {
  padding: 24px;
}

.tab-pane {
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

@media (max-width: 768px) {
  .settings-page {
    padding: 16px;
  }

  .fixed-header h1 {
    font-size: 20px;
  }
}
</style>