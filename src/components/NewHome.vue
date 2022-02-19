<template>
  <div class="home container">
    <!-- header -->
    <div class="header flex">
      <div class="left flex flex-column">
        <h1>Invoices</h1>
        <span>There are 4 items</span>
      </div>
      <div class="left flex flex-row">
        <div @click="toggleFilterMeny" class="filer flex" ref="filter">
          <span>Filter by status</span>
          <img src="../assets/icon-arrow-down.svg" alt="" />
          <ul v-show="filterMenu" class="filter-menu">
            <li>Draft</li>
            <li>Pending</li>
            <li>Paid</li>
            <li>Clear filter</li>
          </ul>
        </div>
        <div @click="newInvoice" class="button flex">
          <div class="inner-button flex">
            <img src="../assets/icon-plus.svg" alt="" />
            <span>New invoice</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "NewHome",
  components: {},
};
</script>

<style lang="scss" scoped>
.home {
  color: #fff;

  .header {
    margin-bottom: 65px;

    .left,
    .right {
      flex: 1;
    }

    .right {
      justify-content: flex-end;
      align-items: center;

      .button,
      .filter {
        align-items: center;

        span {
          font-size: 12px;
        }
      }

      .filter {
        position: relative;
        margin-right: 40px;

        span,
        img {
          pointer-events: none;
        }

        img {
          margin-left: 12px;
          width: 9px;
          height: 5p;
        }
      }
    }
  }
}
</style>
