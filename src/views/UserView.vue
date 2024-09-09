<template>
  <div class="container">
    <div>
      <div class="text-center py-3 col-md-6" style="margin-left: 23%">
        <h3>My Exercise Record</h3>
        <div class="row">
          <div class="col-md-6" style="margin-left: 10%">
            <MapView />
          </div>
        </div>
        <UserSignView />
      </div>
    </div>
    <div class="text-center py-3">
      <h3 class="mb-3">My Record List</h3>
      <UserListView />
    </div>
  </div>
</template>

<script setup>
import UserListView from "../components/user/UserListView.vue";
import UserSignView from "../components/user/UserSignView.vue";
import MapView from "../components/user/MapView.vue";
</script>

<style lang="scss" scoped></style>
