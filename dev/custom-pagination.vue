<template>
  <div class="vgt-wrap__footer vgt-clearfix">
    <div class="footer__navigation vgt-pull-left">

    <a
        href="javascript:undefined"
        class="footer__navigation__page-btn"
        :class="{ disabled: !prevIsPossible }"
        @click.prevent.stop="previousPage"
        tabindex="0">
        <span>PREVIOUS</span>
      </a>
      <a href="javascript:undefined" class="footer__navigation__page-btn"
         :class="{ disabled: !nextIsPossible }" @click.prevent.stop="nextPage" tabindex="0">
        <span>NEXT</span>
      </a>
    </div>

    <div class="footer__row-count vgt-pull-left">
        <div class="footer__navigation__info">{{paginatedInfo}} | </div>
      <span class="footer__row-count__label">SHOW</span>
      <select
        autocomplete="off"
        name="perPageSelect"
        class="footer__row-count__select"
        v-model="currentPerPage"
        @change="handlePerPageChanged">
        <option value="5">5</option>
        <option value="10">10</option>
        <option value="20">20</option>
        <option value="-1">ALL</option>
      </select>
      <span class="footer__row-count__label">ITEMS</span>
    </div>


  </div>
</template>

<script>
import cloneDeep from 'lodash.clonedeep';

export default {
  name: 'custom-pagination',
   props: {
    total: { default: null },
    pageChanged: Function,
    perPageChanged: Function,
  },

  data: () => ({
    currentPage: 1,
    currentPerPage: 5,
    rowsPerPageOptions: [5, 10, 20],
  }),


  computed: {
    currentPerPageString() {
      return this.currentPerPage === -1 ? 'All' : this.currentPerPage;
    },

    paginatedInfo() {
      if (this.currentPerPage === -1) {
        return `DISPLAY ITEMS: 1 - ${this.total}, TOTAL: ${this.total}`;
      }
      let first = ((this.currentPage - 1) * this.currentPerPage) + 1 ?
        ((this.currentPage - 1) * this.currentPerPage) + 1 : 1;

      if (first > this.total) {
        // this probably happened as a result of filtering
        first = 1;
        this.currentPage = 1;
      }

      const last = Math.min(this.total, this.currentPerPage * this.currentPage);
      return `DISPLAY ITEMS: ${first} - ${last}, TOTAL: ${this.total}`;
    },
    nextIsPossible() {
      return this.currentPerPage === -1 ?
        false : (this.total > this.currentPerPage * this.currentPage);
    },
    prevIsPossible() {
      return this.currentPage > 1;
    },
  },

  methods: {
    changePage(pageNumber) {
      if (pageNumber > 0 && this.total > this.currentPerPage * pageNumber) {
        this.currentPage = pageNumber;
        this.handlePageChanged();
      }
    },

    nextPage() {
      if (this.currentPerPage === -1) return;
      if (this.nextIsPossible) {
        ++this.currentPage;
        this.handlePageChanged();
      }
    },

    previousPage() {
      if (this.currentPage > 1) {
        --this.currentPage;
        this.handlePageChanged();
      }
    },

    handlePageChanged() {
      this.pageChanged({ currentPage: this.currentPage });
    },

    handlePerPageChanged(event) {
      if (event) {
        this.currentPerPage = parseInt(event.target.value, 10);
      }
      this.perPageChanged({ currentPerPage: this.currentPerPage })
    },

  },

  mounted() {
      this.handlePerPageChanged();
  },
};
</script>

<style lang="scss">
</style>