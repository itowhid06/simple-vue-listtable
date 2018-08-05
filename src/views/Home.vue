<template>
    <div class="home">
        <TableData msg="Simple ListTable App"/>
    </div>
</template>

<script>
// @ is an alias to /src
import TableData from '@/components/TableData.vue'

export default {
  name: 'home',
  components: {
    TableData
  }
}
</script>
