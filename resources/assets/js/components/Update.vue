<template>
    <div class="row">
        <main-menu></main-menu>
        <div class="grid-x">
            <div class="small-3 columns"></div>
            <div class="small-6 columns">

                <form @submit.prevent="update" method="post">
                    <div v-if="msg.success" class="green">
                        <i class="fi-check"></i>&nbsp;&nbsp;{{msg.success}}
                    </div>
                    <div v-if="msg.error" class="red">
                        <i class="fi-info"></i>&nbsp;&nbsp;{{msg.error}}
                    </div>

                    <div class="form-group">
                        <label for="title">Name</label>
                        <input type="text" required class="form-control" v-model="item.title" required id="title" aria-describedby="title" placeholder="Product Name">
                    </div>
                    <div class="form-group">
                        <label for="price">Price</label>
                        <input type="number" required class="form-control" min="0" v-model="item.price" required id="price" aria-describedby="price" placeholder="Product Price">
                    </div>
                    <div class="form-group">
                        <label for="body">Description</label>
                        <textarea required rows="5" class="form-control" v-model="item.description" required id="body" placeholder="Product Description"></textarea>
                    </div>
                    <button type="submit" class="button primary expanded"><i class="fi-plus"></i>&nbsp;UPDATE {{item.title}}</button>
                </form>
            </div>
            <div class="small-3 columns"></div>
        </div>
        <footer-view></footer-view>
    </div>
</template>
<script>
    import MainMenu from "./MenuLogged.vue"
    import Auth from '../store/auth'
    import Msg from '../helpers/msg'
    import {post} from '../helpers/api'
    import Items from './Items.vue'
    import Footer from './Footer.vue'
    export default {
        data(){
            return {
                auth: Auth.state,
                msg: Msg.state,
                item:{}
            }
        },
        components:{
            'main-menu': MainMenu,
            'footer-view': Footer
        },
        created() {
            this.getItem();
            Auth.initialize();
            if (this.auth.role === 'Admin') {
                //DO Nothing
            } else {
                this.$router.push('/shop')
            }
        },
        methods:{
            getItem(){
                this.$http.get('api/items/' + this.$route.params.item)
                    .then(response => {
                        this.item = response.body
                    })
            },
            update(){
                this.$http.put('api/items/' + this.$route.params.item, this.item)
                    .then(response => {
                        if (response.data) {
                            Msg.setSuccess(this.item.title + ' Has been successfully Updated!');
                            this.$router.push('/shop')
                        }
                    })
                    .catch((err) => {
                        if (err.response.status === 422) {
                            this.error = err.response.data;
                            Msg.setError('There was a problem updating the item, please try again!!')
                        }
                    })
            }
        }

    }
</script>