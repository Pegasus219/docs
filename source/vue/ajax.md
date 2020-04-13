### vue-ajax 使用

#### 一、安装axios组件
    cnpm install axios

#### 二、在main.js中加载axios和qs
    import Axios from 'axios'
    import Qs from 'qs'
    
    Vue.prototype.$axios = Axios
    Vue.prototype.$qs = Qs

#### 三、在组件页面上使用ajax
    <template>
        <div>
            <p>Ajax子页面</p>
            <div>
                <input v-model="inputVal"/>
                <button @click="ajaxSubmit">ajax提交</button>
            </div>
            <ul>
                <li v-for="item in items">{{item.Name}}</li>
            </ul>
        </div>
    </template>

    <script>
    export default {
        data () {
            return {
                inputVal: '',
                items: []
            }
        },
        methods: {
            ajaxSubmit: function () {
                var postVal = this.$qs.stringify({
                    id: this.inputVal
                })
                //☆以下是vue-ajax实现
                this.$axios.post('http://127.0.0.1/ajax', postVal)
                .then(ret => (
                    this.items = ret.data.list
                ))
                .catch(function (err) {
                    console.log(err)
                })
            }
        },
        mounted() {
            //☆页面加载完成后自动ajax
            this.$nextTick(function () {
                this.ajaxSubmit()
            })
        }
    }
    </script>