<template>
    <aside>
        <div class="item">
            <div class="item-content-block">
                <div class="block-title">Select Companies</div>
            </div>
            <div class="item-content-block tags">
                <span style="margin-right: 5px"
                      v-for="item in companies"
                      @click="selectedCompany(item) "
                      :class="selectedCompanies == item ? 'badge badge-primary': 'badge badge-secondary'">
                    {{item}}
                </span>
            </div>
        </div>
    </aside>
</template>
<script>
    //export needed data
    module.exports = {
        props: ['selected'],
        data() {
            return {
                width: 0,
                companies: [],
                selectedCompanies: '',
                checkedCompanies: []
            };
        },
        //run when component is mounted (add resize event)
        mounted() {
            this.getAllCompany();
            this.selectedCompany = selected
        },
        methods: {
            selectedCompany(item){
                this.selectedCompanies = item;
                this.$emit('getdata',item)
            },
            //computes chart dimensions based on page width
            getAllCompany(){
                let url = 'http://localhost:3000/stock/company';
                this.$http
                    .get(url)
                    .then(result => {
                        this.companies = result.data.data[0].attributes;
                    })
                    .catch(this.handleErrors);
            },
        },
        computed: {
            filteredPlaces: function () {
                return this.posts.filter(post =>
                    post.eat_categories.some(tag =>
                        this.checkedCompanies.includes(tag.id)
                    )
                )
            }
        }
    };
</script>
<style scoped>
</style>