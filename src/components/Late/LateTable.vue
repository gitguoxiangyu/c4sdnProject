<template>
    <div class="table">
        <el-table :data="data" style="width: 100%" :row-style="{ height: '45px' }"
            :cell-style="{ fontSize: '14px', textAlign: 'center' }"
            :header-cell-style="{ textAlign: 'center', background: 'rgb(248, 248, 249)' }"
            @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55" />
            <el-table-column prop="student_number" label="学号" />
            <el-table-column prop="name" label="姓名" />
            <el-table-column prop="date" label="晚归时间" />
            <el-table-column prop="dormitory" label="宿舍楼" />
            <el-table-column prop="room" label="房间" />
            <el-table-column fixed="right" label="操作">
                <template #default="scope">
                    <el-button type="danger" @click="handleDelete(scope.row.id)" size="small">
                        <el-icon>
                            <Close />
                        </el-icon>
                        删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <div style="display: flex; flex-direction: row-reverse; margin-right: 20px;">
            <el-pagination background layout="prev, pager, next" :total="totalNum" @current-change="handleCurrentChange" />
        </div>

    </div>
</template>

<script setup>
import { ref, watch } from "vue";

const emit = defineEmits(['SectionChange', 'getData', 'pageChange'])
const props = defineProps(['data', 'totalNum'])

const multipleSelection = ref([])
const handleSelectionChange = (val) => {
    multipleSelection.value = val
}
watch(multipleSelection, (value) => {
    emit('SectionChange', value)
})

const handleDelete = (id) => {
    console.log(id);
    emit('getData');
}
const handleCurrentChange = (value) => {
    emit('pageChange', value)
}
</script>

<style scoped lang="less"></style>