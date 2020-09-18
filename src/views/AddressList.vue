<template>
    <div>
        <van-address-list
                v-model="chosenAddressId"
                :list="list"
                default-tag-text="默认"
                @add="onAdd"
                @edit="onEdit"
                @select="onselect"
        />
    </div>
</template>

<script>
    import { Toast } from 'vant';
    export default {
        data() {
            return {
                chosenAddressId: 0,
                list: [
                    {
                        areaCode: "440303",
                        id: 21,
                        name: "冯斌",
                        tel: "15357481218",
                        address: "安徽省六安市舒城县鼓楼新城"
                    },
                    {
                        areaCode: "045512",
                        id: 22,
                        name: "冯斌",
                        tel: "15357481218",
                        address: "湖南省湘潭市湘潭大学"
                    }
                ]
            }
        },
        created(){
            const _this = this
            axios.get('http://localhost:8181/address/list').then(function (resp) {
                _this.list = resp.data.data
            })
        },
        methods: {
            onAdd() {
                this.$router.push('/addressNew')
            },
            onEdit(item) {
                //将对象封装成String格式传递给address
                let data = JSON.stringify(item)
                //不能？传参，将item绑定到data传递给addressEdit
                this.$router.push({path:'/addressEdit',query:{item:data}})
            },
            onselect(item){
                let orderForm = {
                    name: item.name,
                    tel: item.tel,
                    address: item.address,
                    specsId: this.$store.state.specsId,
                    quantity: this.$store.state.quantity
                }
                const _this = this
                axios.post('http://localhost:8181/order/create',orderForm).then(function (resp) {
                    if(resp.data.code == 0){
                        let instance = Toast('下单成功');
                        setTimeout(() => {
                            instance.close();
                            _this.$router.push('/detail?orderId='+resp.data.data.orderId)
                        }, 1000)
                    }
                })
            }
        }
    }
</script>