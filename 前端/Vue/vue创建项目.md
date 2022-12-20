vue create xxxxx


设置局部变量
```vue
<template slot-scope="scope">
  <!-- handleUpdateClick($event,id) -->
  <el-button type="primary" @click(updateDialogVisible=true)" :set(scope.a=true)">{{scope.a}}el-button>
  <el-button type="warning@click="handlePauseOrResumeClick"></el-button>
  <el-button type="danger@click="handleDeleteClick">删除el-button>
</template>
```