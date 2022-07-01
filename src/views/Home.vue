<template>
  <div class="container">
    <div class="d-flex justify-content-between align-items-center py-4 mb-2">
      <div class="w-25">
        <router-link to="/" class="btn btn-sm bg-white">
          <ArrowLeftIcon style="width: 30px;height: 30px"></ArrowLeftIcon>
        </router-link>
      </div>
      <div class="w-50 text-center">
        <h5>Daftar Bisnis</h5>
      </div>
      <div class="w-25 text-end">
        <router-link to="/" class="btn btn-sm bg-white">
          <ChatAltIcon style="width: 30px;height: 30px"></ChatAltIcon>
        </router-link>
      </div>
    </div>
    <div class="d-flex align-items-center mb-3">
      <div class="input-group flex-1">
        <span class="input-group-text bg-light border-light" id="basic-addon1">
          <SearchIcon style="width: 20px;height: 20px"></SearchIcon>
        </span>
        <input type="text" class="form-control bg-light border-start-0 border-light" placeholder="Cari nama bisnis" v-model="filter.keyword" @change="nextPage(1)">
      </div>
      <div class="w-auto ps-3">
        <button class="btn btn-outline-dark btn-sm d-inline-flex align-items-center" data-bs-toggle="modal"
          data-bs-target="#modalFilter">
          <MenuAlt1Icon style="width: 25px;height: 25px" class="me-2"></MenuAlt1Icon>
          Kategori
        </button>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-md-4 mb-4" v-for="item in items" :key="item">
        <div class="card">
          <div class="card-body">
            <img :src="item.logoUrl" :alt="item.name" style="width: 100%">
          </div>
          <div class="card-body">
            <h4 style="display: -webkit-box;
                        -webkit-line-clamp: 2;
                        -webkit-box-orient: vertical;
                        overflow: hidden;height:60px;">{{ item.businessName }}</h4>
            <small class="text-muted">
              {{ item.businessCategoryName }}
            </small>
          </div>
        </div>
      </div>
    </div>
    <div class="d-flex align-items-center justify-content-between mb-4">
      <span>
        Halaman {{ currentPage }} dari {{ totalPage }}
      </span>
      <nav aria-label="...">
        <ul class="pagination justify-content-end">
          <li class="page-item" :class="{ 'disabled': currentPage == 1 }">
            <button class="page-link" @click="nextPage(currentPage - 1)">Previous</button>
          </li>
          <li v-for="page in totalPage" :key="page" class="page-item" :class="{ 'active': page == currentPage }">
            <button class="page-link" @click="nextPage(page)">{{ page }}</button>
          </li>
          <li class="page-item" :class="{ 'disabled': currentPage == totalPages }">
            <button class="page-link" @click="nextPage(currentPage + 1)">Next</button>
          </li>
        </ul>
      </nav>
    </div>
  </div>
  <div class="modal fade" id="modalFilter" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1"
    aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered modal-lg">
      <div class="modal-content">
        <div class="modal-header border-bottom-0">
          <h5 class="modal-title" id="exampleModalLabel">Filter</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <small class="text-muted d-block mb-4">Kategori Bisnis</small>
          <div class="form-check d-flex align-items-center justify-content-between mb-2 p-3" v-for="category in categoryItems"
            :key="category">
            <label class="form-check-label d-block w-100" :for="'check-' + category.paramCode">
              {{ category.paramName }}
            </label>
            <input class="form-check-input float-none ms-0" type="checkbox" :value="category.paramCode" :id="'check-' + category.paramCode" v-model="filter.categoryItems">
          </div>
        </div>
        <div class="modal-footer border-top-0">
          <button type="button" class="btn bg-dark text-white btn-block w-100" @click="nextPage(1)" data-bs-dismiss="modal">Terapkan</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios'

import { ArrowLeftIcon, SearchIcon, MenuAlt1Icon } from "@heroicons/vue/outline"
import { ChatAltIcon } from "@heroicons/vue/solid"

export default {
  name: 'Home',
  components: {
    ArrowLeftIcon,
    ChatAltIcon,
    SearchIcon,
    MenuAlt1Icon
  },
  data() {
    return {
      filter: {
        keyword: '',
        categoryItems: []
      },
      currentPage: 1,
      totalPage: 0,
      items: [],
      categoryItems: []
    }
  },
  created(){
    this.renderCategory()
    this.renderItems()
  },
  computed: {
    totalPages(){
      return this.totalPage
    }
  },
  methods: {
    nextPage(page = 1) {
      this.currentPage = page
      this.renderItems()
    },
    renderItems() {
      let that = this
      axios.post('http://sandbox.bizharedev.id:17001/business/parent/all', {
        businessName: this.filter.keyword,
        size: 10,
        page: this.currentPage,
        listCategory: this.filter.categoryItems
      })
      .then(res => res.data)
      .then(function(res){
        if(res.success)
        {
          let data = res.data
          that.items = data.content
          that.totalPage = data.totalPages
        }
      })
    },
    renderCategory() {
      let that = this
      axios.get('http://sandbox.bizharedev.id:17001/media/param/business/category')
      .then(res => res.data)
      .then(function(res){
        if(res.success)
        {
          that.categoryItems = res.data
        }
      })
    }
  }
}
</script>
<style>
.form-control:focus,
.page-link:focus,
.btn:focus {
  box-shadow: none;
}

.form-check:hover {
  background: var(--bs-light);
}
</style>