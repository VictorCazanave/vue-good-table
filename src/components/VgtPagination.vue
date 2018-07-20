<template>
  <div class="vgt-wrap__footer vgt-clearfix">
    <div class="footer__row-count vgt-pull-left">
      <span class="footer__row-count__label">{{rowsPerPageText}}</span>
      <select
        autocomplete="off"
        name="perPageSelect"
        class="footer__row-count__select"
        v-model="currentPerPage"
        @change="perPageChanged">
        <option
          v-for="(option, idx) in rowsPerPageOptions"
          v-bind:key="'rows-dropdown-option-' + idx"
          :value="option">
          {{ option }}
        </option>
        <option v-if="paginateDropdownAllowAll" :value="total">{{allText}}</option>
      </select>
    </div>
    <div class="footer__navigation vgt-pull-right">
      <a
        href="javascript:undefined"
        class="footer__navigation__page-btn"
        :class="{ disabled: !prevIsPossible }"
        @click.prevent.stop="previousPage"
        tabindex="0">
        <span class="chevron" v-bind:class="{ 'left': !rtl, 'right': rtl }"></span>
        <span>{{prevText}}</span>
      </a>
      <div class="footer__navigation__info">{{paginatedInfo}}</div>
      <a href="javascript:undefined" class="footer__navigation__page-btn"
         :class="{ disabled: !nextIsPossible }" @click.prevent.stop="nextPage" tabindex="0">
        <span>{{nextText}}</span>
        <span class="chevron" v-bind:class="{ 'right': !rtl, 'left': rtl }"></span>
      </a>
    </div>
  </div>
</template>

<script>
import cloneDeep from 'lodash.clonedeep';

const DEFAULT_PAGE = 1;
const DEFAULT_PER_PAGE = 10;
const DEFAULT_ROWS_PER_PAGE_DROPDOWN = [10, 20, 30, 40, 50];

export default {
  name: 'VgtPagination',
  props: {
    styleClass: { default: 'table table-bordered' },
    total: { default: null },
    page: {},
    perPage: {},
    rtl: { default: false },
    customRowsPerPageDropdown: { default() { return []; } },
    paginateDropdownAllowAll: { default: true },

    // text options
    nextText: { default: 'Next' },
    prevText: { default: 'Prev' },
    rowsPerPageText: { default: 'Rows per page:' },
    ofText: { default: 'of' },
    allText: { default: 'All' },
  },

  data: () => ({
    currentPage: DEFAULT_PAGE,
    currentPerPage: DEFAULT_PER_PAGE,
    rowsPerPageOptions: [],
  }),

  watch: {
    page() {
      this.handlePage();
      this.pageChanged();
    },

    perPage() {
      this.handlePerPage();
      this.perPageChanged();
    },

    customRowsPerPageDropdown() {
      if (this.customRowsPerPageDropdown !== null
        && (Array.isArray(this.customRowsPerPageDropdown)
        && this.customRowsPerPageDropdown.lenght !== 0)) {
        this.rowsPerPageOptions = this.customRowsPerPageDropdown;
      }
    },
  },

  computed: {
    pagesCount() {
			const quotient = Math.floor(this.total / this.currentPerPage);
			const remainder = this.total % this.currentPerPage;

			return remainder === 0 ? quotient : quotient + 1;
		},
    paginatedInfo() {
      const first = ((this.currentPage - 1) * this.currentPerPage) + 1;
      const last = Math.min(this.total, this.currentPage * this.currentPerPage);

      return `Display items: ${first}-${last},Total: ${this.total}`;
    },
    nextIsPossible() {
      return this.currentPage < this.pagesCount;
    },
    prevIsPossible() {
      return this.currentPage > 1;
    },
  },

  methods: {
    // optionSelected(option) {
    //   return this.currentPerPage === option;
    // },

    // Not used
    // reset() {},

    // Not used
    //changePage(pageNumber) {
    // if (pageNumber > 0 && this.total > this.currentPerPage * pageNumber) {
    //   this.currentPage = pageNumber;
    //   this.pageChanged();
    // }
    //},

    nextPage() {
      if (this.nextIsPossible) {
				++this.currentPage;
				this.pageChanged();
			}
    },

    previousPage() {
      if (this.prevIsPossible) {
        --this.currentPage;
        this.pageChanged();
      }
    },

    pageChanged() {
      this.$emit('page-changed', { currentPage: this.currentPage });
    },

    perPageChanged() {
      // Go to 1rst page
      this.currentPage = 1;
      this.$emit('per-page-changed', { currentPerPage: this.currentPerPage });
    },

    handlePerPage() {
      this.rowsPerPageOptions = cloneDeep(DEFAULT_ROWS_PER_PAGE_DROPDOWN);

      if (this.perPage) {
        // if perPage doesn't already exist, we don't use it
        let found = false;
        for (let i = 0; i < this.rowsPerPageOptions.length; i++) {
          if (this.rowsPerPageOptions[i] === this.perPage) {
            found = true;
          }
        }
        this.currentPerPage = found ? this.perPage : DEFAULT_PER_PAGE;
      } else {
        // reset to default
        this.currentPerPage = DEFAULT_PER_PAGE;
      }

      if (this.customRowsPerPageDropdown !== null
        && (Array.isArray(this.customRowsPerPageDropdown)
        && this.customRowsPerPageDropdown.length !== 0)) {
        this.rowsPerPageOptions = this.customRowsPerPageDropdown;
      }
    },

    handlePage() {
      if (this.page) {
        this.currentPage = this.page;
      }
    },
  },

  mounted() {
    this.handlePerPage();
    this.handlePage();
  },
};
</script>

<style lang="scss">

</style>
