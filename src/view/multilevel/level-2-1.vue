<template>
    <div>
        <Button class="new" type="primary" @click="add">新增</Button>
        <Table border :columns="table_col" :data="table_data"></Table>
        <Divider plain>
            <Page style="text-align:left" :current="current" :total="totalCount" :page-size="pageSize" :page-size-opts="[5,10]" show-total show-sizer show-elevator @on-change="changepage" @on-page-size-change="changepagesize" />
        </Divider>
        <Drawer title="设备配置" v-model="myDrawer" width="500" :mask-closable="false">
            <Form :model="formData">
                <Row :gutter="32">
                    <Col span="10">
                    <FormItem v-if="createDrawer" label="设备名称">
                        <Input v-model="formData.equipmentName" placeholder="请输入设备名称" />
                    </FormItem>
                    <FormItem v-if="modifyDrawer" label="设备名称">
                        <Input v-model="formData.equipmentName" placeholder="请输入设备名称" />
                    </FormItem>
                    </Col>
                    <Col span="10">
                    <FormItem v-if="createDrawer" label="设备内容">
                        <Input v-model="formData.content" placeholder="请输入设备内容" />
                    </FormItem>
                    <FormItem v-if="modifyDrawer" label="设备内容">
                        <Input v-model="formData.content" placeholder="请输入设备内容" />
                    </FormItem>
                    </Col>
                </Row>
            </Form>
            <div class="drawer-footer" v-if="createDrawer">
                <Button style="margin-right: 8px" @click="myDrawer = false">取消</Button>
                <Button type="primary" @click="createSubmit">提交</Button>
            </div>

            <div class="drawer-footer" v-if="modifyDrawer">
                <Button style="margin-right: 8px" @click="myDrawer = false">取消</Button>
                <Button type="primary" @click="modifySubmit">更改</Button>
            </div>
        </Drawer>
    </div>
</template>
<script>
    export default {
        name: 'level_2_1',
        data() {
            return {
                // api
                default_api: 'http://127.0.0.1:8080',

                // 翻页
                totalCount: 0,
                pageSize: 5,
                current: 1,

                // table
                table_col: [{
                        title: '序号',
                        type: 'index',
                        width: 80,
                        align: 'center'
                    },
                    {
                        title: '设备名称',
                        key: 'equipmentName',
                        align: 'center'
                    },
                    {
                        title: '设备内容',
                        key: 'content',
                        align: 'center'
                    },
                    {
                        title: '操作',
                        width: 210,
                        align: 'center',
                        render: (h, { row, index }) => {
                            return [
                                h('Button', {
                                    props: {
                                        type: 'primary',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.show(row, index)
                                        }
                                    }
                                }, '详情'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    style: {
                                        marginLeft: '2px'
                                    },
                                    on: {
                                        click: () => {
                                            this.remove(row, index)
                                        }
                                    }
                                }, '删除')
                            ]
                        }
                    }
                ],
                table_data: [],
                all_table_data: [],

                // drawer
                myDrawer: false,
                createDrawer: false,
                modifyDrawer: false,

                formData: {
                    id: 0,
                    equipmentName: '',
                    content: ''
                }
            }
        },
        methods: {
            clearDrawer() {
                for (let key in this.formData) {
                    this.formData[key] = ''
                }
                this.modify = false
                this.createDrawer = false
                this.modifyDrawer = false
            },
            showAll() {
                this.clearDrawer()
                const api = this.default_api + '/equipment/getall'
                this.axios.get(api)
                    .then(response => {
                        let res = response.data['data']
                        this.all_table_data = res

                        // 页码相关
                        this.totalCount = res.length
                        this.current = 1
                        const start = 1
                        const last = this.pageSize
                        const end = last <= this.totalCount ? last : this.totalCount
                        this.table_data = this.all_table_data.slice(start - 1, end)
                    })
                    .catch(error => {
                        console.log(error);
                    });
            },
            // 翻页
            changepage(index) {
                // index为当前页码数
                this.current = index
                this.table_data = []
                const start = (index - 1) * this.pageSize + 1
                const last = index * this.pageSize
                const end = last <= this.totalCount ? last : this.totalCount
                this.table_data = this.all_table_data.slice(start - 1, end)
            },
            changepagesize(pageSize) {
                this.current = 1
                this.pageSize = pageSize
                this.changepage(this.current)
            },

            // 首页的三个按钮
            // 新增
            add() {
                this.clearDrawer()
                this.myDrawer = true
                this.createDrawer = true
            },
            // 详情
            show(row, index) {
                this.clearDrawer()
                this.myDrawer = true
                this.modifyDrawer = true
                const api = this.default_api + '/equipment/getall'
                this.axios.get(api)
                    .then(response => {
                        const curindex = (this.current - 1) * this.pageSize + index
                        let res = response.data['data'][curindex]
                        this.formData.id = res['id']
                        this.formData.equipmentName = res['equipmentName']
                        this.formData.content = res['content']
                    })
                    .catch(error => {
                        console.log(error);
                    });
            },
            // 删除
            remove(row, index) {
                console.log(row)
                this.$Modal.confirm({
                    title: '删除提示',
                    content: '是否确定删除?',
                    okText: '确定',
                    cancelText: '取消',

                    // 确定删除 大写O小写k
                    onOk: () => {
                        // 更新数据库
                        const id = row.id
                        const api = this.default_api + '/equipment/del' + '?equipmentID=' + id
                        this.axios.get(api).then(res => {
                            // 更新表格数据
                            this.table_data.splice(index, 1)
                            this.$Message.success('删除成功')
                            this.current = 1
                            this.showAll()
                        })
                    }
                })
            },

            // drawer里的按钮
            // 提交
            createSubmit() {
                const api = this.default_api + '/equipment/add'
                const data = JSON.stringify({
                    equipmentName: this.formData.equipmentName,
                    content: this.formData.content
                })
                this.$Modal.confirm({
                    title: '新增提示',
                    content: '是否确定新增设备?',
                    okText: '确定',
                    cancelText: '取消',
                    onOk: () => {
                        this.axios.post(api, data).then(() => {
                            this.myDrawer = false
                            this.$Message.success('新增成功')
                            this.showAll()
                        })
                    }
                })
            },
            //更改
            modifySubmit() {
                const api = this.default_api + '/equipment/update'
                const data = JSON.stringify({
                    id: this.formData.id,
                    equipmentName: this.formData.equipmentName,
                    content: this.formData.content
                })
                this.$Modal.confirm({
                    title: '修改提示',
                    content: '是否确定修改设备?',
                    okText: '确定',
                    cancelText: '取消',
                    onOk: () => {
                        this.axios.put(api, data).then(() => {
                            this.myDrawer = false
                            this.$Message.success('修改成功')
                            this.showAll()
                        })
                    }
                })
            },
        },
        mounted() {
            this.showAll()
        }

    }
</script>
<style>
    .new {
        margin-bottom: 10px;
    }

    .drawer-footer {
        width: 100%;
        position: absolute;
        bottom: 0;
        left: 0;
        border-top: 1px solid #e8e8e8;
        padding: 10px 16px;
        text-align: right;
        background: #fff;
    }
</style>