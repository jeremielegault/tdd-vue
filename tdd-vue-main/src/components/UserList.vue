<template>
  <Card>
    <template v-slot:header>
      <h3>{{ $t("users") }}</h3>
    </template>
    <template v-slot:default>
      <ul class="list-group list-group-flush">
        <li
          class="list-group-item list-group-item-action"
          v-for="user in page.content"
          @click="$router.push('/user/' + user.id)"
          :key="user.id"
        >
          <UserListItem :user="user" />
        </li>
      </ul>
    </template>
    <template v-slot:footer>
      <button
        class="btn btn-outline-secondary btn-sm float-start"
        @click="loadData(page.page - 1)"
        v-show="page.page !== 0 && !pendingApiCall"
      >
        {{ $t("previousPage") }}
      </button>
      <button
        class="btn btn-outline-secondary btn-sm float-end"
        @click="loadData(page.page + 1)"
        v-show="page.totalPages > page.page + 1 && !pendingApiCall"
      >
        {{ $t("nextPage") }}
      </button>
      <Spinner size="normal" v-show="pendingApiCall" />
    </template>
  </Card>
</template>
<script>
import { loadUsers } from "../api/apiCalls";
import UserListItem from "./UserListItem";
import Spinner from "./Spinner";
import Card from "./Card";
export default {
  components: {
    UserListItem,
    Spinner,
    Card,
  },
  data() {
    return {
      page: {
        content: [],
        page: 0,
        size: 0,
        totalPages: 0,
      },
      pendingApiCall: true,
    };
  },

  async mounted() {
    this.loadData();
  },
  methods: {
    async loadData(pageIndex) {
      this.pendingApiCall = true;
      const response = await loadUsers(pageIndex);
      this.page = response.data;
      this.pendingApiCall = false;
    },
  },
};
</script>

<style scoped>
li {
  cursor: pointer;
}
</style>