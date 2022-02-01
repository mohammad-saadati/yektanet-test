<template>
  <div class="hello">
    <el-row :gutter="10">
      <el-col style="margin-bottom:1rem;" :xs="12" :sm="6">
        <el-input v-model="creator" placeholder="نام تغییردهنده"/>
      </el-col>
      <el-col style="margin-bottom:1rem;" :xs="12" :sm="6">
        <el-input v-model="date" placeholder="تاریخ"/>
      </el-col>
      <el-col style="margin-bottom:1rem;" :xs="12" :sm="6">
        <el-input v-model="adsName" placeholder="نام آگهی"/>
      </el-col>
      <el-col style="margin-bottom:1rem;" :xs="12" :sm="6">
        <el-input v-model="field" placeholder="فیلد"/>
      </el-col>
    </el-row>

    <el-table
      :data="tableData"
      stripe 
      align="right"
      header-align="right"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" />
      <el-table-column prop="name" label="نام تغییردهنده" sortable width="180" />
      <el-table-column prop="date" label="تاریخ" sortable width="180" />
      <el-table-column prop="title" label="نام آگهی" sortable width="180" />
      <el-table-column prop="field" label="فیلد" sortable/>
      <el-table-column prop="old_value" label="مقدار قدیمی" sortable/>
      <el-table-column prop="new_value" label="مقدار جدید" sortable/>
    </el-table>
  </div>
</template>

<script>
import { useRouter, useRoute } from 'vue-router'
import { ref, watch, computed, onMounted} from 'vue'
import json from '@/assets/data.json'

export default {
  setup() {
    const router = useRouter()
    const route = useRoute()

    const creator = ref();
    const date = ref();
    const adsName = ref();
    const field = ref();

    const filteredData = ref()
    const data = ref([]);
    data.value = json;

    const tableData = computed(() => {
      if(!creator.value && !adsName.value && !field.value && !date.value) return data.value
      return filteredData.value
    })

    onMounted(() => {
      creator.value = route.query?.creator
      date.value = route.query?.date
      adsName.value = route.query?.adsName
      field.value = route.query?.field
      
      filter()
    })

    function filter() {
      filteredData.value = data.value.filter( item => {
        let temp = []
        temp.push(field.value ? item.field.includes(field.value) : 1)
        temp.push(adsName.value ? item.title.toLowerCase().includes(adsName.value.toLowerCase()) : 1)
        temp.push(date.value ? item.date.includes(date.value) : 1)
        temp.push(creator.value ? item.name.toLowerCase().includes(creator.value.toLowerCase()) : 1)
        
        return temp.includes(false) ? false : true;
      })
    }

    function handleSelectionChange(value) {
      console.log(value)
    }

    watch(creator, (creator) => {
      const query = { ...route.query, creator: creator };
      router.replace({ query })
    })

    watch(date, (date) => {
      const query = { ...route.query, date: date };
      router.replace({ query });
    })
    
    watch(adsName, (adsName) => {
      const query = { ...route.query, adsName: adsName };
      router.replace({ query })
    })

    watch(field, (field) => {
      const query = { ...route.query, field: field };
      router.replace({ query });
    })

    watch(route, (url) => {
      creator.value = url.query?.creator
      date.value = url.query?.date
      adsName.value = url.query?.adsName
      field.value = url.query?.field
      filter();
    });

    return {
      date,
      field,
      creator,
      adsName,

      data,

      tableData,

      handleSelectionChange
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
.el-table .el-table__cell {
  text-align: right!important;
}
</style>
