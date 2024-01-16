<template>
  <div>
    <div class="text-center ma-2">
      <v-snackbar v-model="snackbar" small top="top" :color="color">
        {{ response }}
      </v-snackbar>
    </div>

    <v-card elevation="1">
      <v-data-table
        dense
        :headers="headers"
        :items="data"
        :loading="loadinglinear"
        :options.sync="options"
        :footer-props="{
          itemsPerPageOptions: [10, 20, 50, 100, 500],
        }"
        class="elevation-1"
        :server-items-length="totalRowsCount"
      >
        <template v-slot:top>
          <v-toolbar flat>
            {{ Model }}s
            <v-icon color="black" @click="getDataFromApi">mdi-reload</v-icon>
            <!-- <v-row no-gutters class="mx-5">
              <v-col cols="2">
                <CompanyList
                  @id="
                    (e) => {
                      filters.company_id = e;
                      getDataFromApi();
                    }
                  "
                />
              </v-col>
            </v-row> -->
            <v-spacer></v-spacer>
            <!-- <v-btn
              dense
              small
              class="primary"
              text
              title="Create Quotation"
              @click="() => $router.push(`/quotation/create`)"
            >
              Create Quotation
              <v-icon right dark>mdi-plus-circle-outline</v-icon>
            </v-btn> -->
          </v-toolbar>
        </template>
      
        <template v-slot:item.description="{ item }">
          <ReadMore :text="item.description" />
        </template>
        <template v-slot:item.status="{ item }">
          <v-chip
            class="mx-2"
            dark
            small
            :color="getRelationStatus(item.status)"
            >{{ item.status }}</v-chip
          >
        </template>

        <template v-slot:item.options="{ item }">
          <v-menu bottom left>
            <template v-slot:activator="{ on, attrs }">
              <v-btn dark-2 icon v-bind="attrs" v-on="on">
                <v-icon>mdi-dots-vertical</v-icon>
              </v-btn>
            </template>
            <v-list width="175" dense>
              <!-- <v-list-item>
                  <v-list-item-title>
                    <QuotationSingle :key="getRandomId()" :item="item" />
                  </v-list-item-title>
                </v-list-item> -->
              <v-list-item>
                <v-list-item-title>
                  <v-dialog v-model="dialog" width="620">
                    <template v-slot:activator="{ on, attrs }">
                      <span
                        style="cursor: pointer"
                        text
                        v-bind="attrs"
                        v-on="on"
                      >
                        <v-icon color="secondary" small> mdi-eye </v-icon>
                        View
                      </span>
                    </template>
                    <div class="white text-right">
                      <v-icon
                        @click="dialog = false"
                        class="mx-3 mt-3"
                        color="primary"
                        >mdi-close-circle-outline</v-icon
                      >
                    </div>
                    <QuotationV1SinglePreviewCard :payload="item" />
                  </v-dialog>
                </v-list-item-title>
              </v-list-item>
              <v-list-item>
                <v-list-item-title>
                  <QuotationV1SinglePrint
                    iconColor="black"
                    :key="getRandomId()"
                    :item="item"
                  />
                </v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
        </template>
      </v-data-table>
    </v-card>
  </div>
</template>

<script>
export default {
  data: () => ({
    dialog: false,
    totalRowsCount: 0,
    showFilters: false,
    filters: {},
    isFilter: false,
    loadinglinear: true,
    attrs: [],
    selectedFile: "",
    color: "background",
    response: "",
    upload: {
      name: "",
    },
    payload: {},
    options: {},
    Model: "Quotation",
    endpoint: "quotation",
    snackbar: false,
    loading: false,
    response: "",
    data: [],
    errors: [],
    id: 30,
    // short_headers: require("../../headers/short-quotation.json"),
    headers: require("../../headers/quotation.json"),
  }),

  async created() {
    this.loading = false;

    this.getDataFromApi();
    //this.getDepartments(options);
  },
  mounted() {},
  watch: {
    options: {
      handler() {
        this.getDataFromApi();
      },
      deep: true,
    },
  },
  methods: {
    getRelationStatus(status) {
      let statusses = {
        pending: "orange",
        submitted: "blue",
        approved: "green",
        cancelled: "red",
        invoiced: "primary",
      };

      return statusses[status];
    },
    getRandomId() {
      return Math.random().toString(36).substring(2);
    },
    priorityRelatedColor(value) {
      let color = {
        High: "red",
        Medium: "blue",
        Low: "grey",
      };
      return color[value];
    },

    statusRelatedColor(value) {
      let color = {
        Open: "green",
        "In Progress": "blue",
        Close: "grey",
      };
      return color[value];
    },
    applyFilters() {
      this.getDataFromApi();
    },
    toggleFilter() {
      // this.filters = {};
      this.isFilter = !this.isFilter;
    },
    clearFilters() {
      this.filters = {};

      this.isFilter = false;
      this.getDataFromApi();
    },
    async getDataFromApi() {
      this.loadinglinear = true;

      let json = {
        key: "quotations",
        options: this.options,
        refresh: true,
        endpoint: this.endpoint,
        filters: this.filters,
      };

      const data = await this.$store.dispatch("fetchData", json);

      this.data = data.data;

      this.totalRowsCount = data.total;
      this.loadinglinear = false;

      this.showCard = true;
      this.cardItem = data.data[0];
    },
    handleSuccessResponse(message) {
      this.snackbar = true;
      this.response = message;
      this.getDataFromApi();
    },
  },
};
</script>
