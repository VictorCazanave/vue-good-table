<template>
  <div class="vgt-wrap__footer vgt-clearfix">
    <div class="footer__navigation vgt-pull-left">

    <a
        href="javascript:undefined"
        class="footer__navigation__page-btn"
        :class="{ disabled: !prevIsPossible }"
        @click.prevent.stop="firstPage">
        <span>FIRST</span>
      </a>
    <a
        href="javascript:undefined"
        class="footer__navigation__page-btn"
        :class="{ disabled: !prevIsPossible }"
        @click.prevent.stop="previousPage">
        <span>PREVIOUS</span>
      </a>
      <form @submit.prevent.stop="changePage">
          <label for="page-number">PAGE</label>
          <input type="number" name="page-number" id="page-number" v-model="pageNumber"><span>/{{totalPagesNumber}}</span>
      </form>
      <a href="javascript:undefined" class="footer__navigation__page-btn"
         :class="{ disabled: !nextIsPossible }" @click.prevent.stop="nextPage">
        <span>NEXT</span>
      </a>
      <a href="javascript:undefined" class="footer__navigation__page-btn"
         :class="{ disabled: !nextIsPossible }" @click.prevent.stop="lastPage">
        <span>LAST</span>
      </a>
    <a href="javascript:undefined" class="footer__navigation__page-btn"
         @click.prevent.stop="refresh">
        <span>REFRESH</span>
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

  data() {
      return {
        currentPage: 1,
        currentPerPage: 5,
        rowsPerPageOptions: [5, 10, 20],
        pageNumber: 1,
    };
  },

  computed: {
    totalPagesNumber() {
        const quotient =  Math.floor(this.total / this.currentPerPage);
        const remainder = this.total % this.currentPerPage;

        return remainder === 0 ? quotient : quotient + 1;
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
      return this.currentPage < this.totalPagesNumber;
    },
    prevIsPossible() {
      return this.currentPage > 1;
    },
  },

  methods: {
    changePage() {
      if (this.pageNumber > 0 && this.pageNumber <= this.totalPagesNumber && this.pageNumber !== this.currentPage) {
        this.currentPage = this.pageNumber;
        this.handlePageChanged();
      }
    },

    nextPage() {
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

    lastPage() {
      if (this.nextIsPossible) {
        ++this.currentPage;
        this.handlePageChanged();
      }
    },

    firstPage() {
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
      refresh() {
      console.log('REFRESH');
      // Use vuex to refresh logs
  },

  },


  created() {
      this.handlePerPageChanged();
  },
};
</script>

<style lang="scss">
</style>