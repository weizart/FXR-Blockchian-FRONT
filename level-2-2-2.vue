<template>
 <div class="demo-split">
   <split v-model="split1">
     <div slot="left" class="semo-split-pane">
       <h2>存证信息登记</h2>
        <Table stripe :columns="columns2" :data="data2"></Table>
        <Form :model="formItem" :label-width="80">
            <FormItem label="作品名称">
                <Input v-model="formItem.input" placeholder="Enter something..."></Input>
            </FormItem>
            <FormItem label="创作城市">
                <Input v-model="formItem.input1" placeholder="Enter something..."></Input>
            </FormItem>
            <FormItem label="创作时间">
                <Input v-model="formItem.input2" placeholder="Enter something..."></Input>
            </FormItem>
            <FormItem label="作品类型">
                 <Select v-model="formItem.select">
                    <Option value="1">文字</Option>
                    <Option value="2">口述</Option>
                    <Option value="3">音乐</Option>
                    <Option value="4">戏曲</Option>
                    <Option value="5">曲艺</Option>
                    <Option value="6">舞蹈</Option>、
                    <Option value="7">杂技</Option>
                    <Option value="8">美术</Option>
                    <Option value="9">建筑</Option>
                    <Option value="10">摄影</Option>
                    <Option value="11">电影</Option>
                    <Option value="12">类似摄制电影方法作品</Option>
                    <Option value="13">工程设计图</Option>
                    <Option value="15">地图</Option>
                    <Option value="17">模型</Option>
                    <Option value="19">录音</Option>
                    <Option value="20">录像</Option>
                    <Option value="181">其他</Option>
                </Select>
            </FormItem>
            <FormItem label="著作权产生方式">
                 <Select v-model="formItem.select1">
                    <Option value="0">个人</Option>
                    <Option value="1">合作</Option>
                    <Option value="2">法人</Option>
                    <Option value="3">职务</Option>
                    <Option value="4">委托</Option>
                </Select>
            </FormItem>
            <FormItem label="创作性质">
                 <Select v-model="formItem.select2">
                    <Option value="0">原创</Option>
                    <Option value="1">改编</Option>
                    <Option value="2">翻译</Option>
                    <Option value="3">汇编</Option>
                    <Option value="4">注释</Option>
                    <Option value="5">整理</Option>
                    <Option value="6">其他</Option>
                </Select>
            </FormItem>
            <FormItem>
                    <Button type="primary">发起存证</Button>
                    <Button style="margin-left: 8px">取消</Button>
                    <Button style="margin-left: 8px">保存</Button>
        </FormItem>
        </Form>
     </div> 
     <div slot="right" class="demo-split-pane">
         <h3>历史记录</h3>
         <Table stripe :columns="columns1" :data="data1"></Table>
     </div>
   </split>
  </div>
</template>
<script>
    export default {
        data () {
            return {
                // api
                default_api: 'http://127.0.0.1:4523/mock/758636',
                split1:0.7,
                formItem: {
                    input: '',
                    input1: '',
                    input2: '',
                    select: '',
                    select1: '',
                    select2: ''
                },
                 columns1: [
                    {
                        title: '创建时间',
                        key: 'create_time'
                    },
                    {
                        title: '更新时间',
                        key: 'update_time'
                    },
                    {
                        title: '审核状态',
                        key: 'audit_status'
                    },
                    {
                        title:'违法状态',
                        key: 'illegal_status'
                    },
                    {
                        title:'侵权状态',
                        key: 'tort_status'
                    }
                ],
                columns2: [
                    {
                        title: '当前状态',
                        key: 'now_status'
                    }
                ],
                data1: [
                    {
                        create_time: 'John Brown',
                        update_time: 18,
                        audit_status: 'New York No. 1 Lake Park',
                        illegal_status: '2016-10-03',
                        tort_status: 'wei'
                    },
                    {
                        create_time: 'John Brown',
                        update_time: 18,
                        audit_status: 'New York No. 1 Lake Park',
                        illegal_status: '2016-10-03',
                        tort_status: 'wei'
                    }
                ],
                data2: [
                    {
                        now_status: '1'
                    }
                ]
            }
        }
    }
</script>
<style>
    .demo-split{
        height: 200px;
        border: 1px solid #dcdee2;
    }
    .demo-split-pane{
        padding: 10px;
    }
</style>