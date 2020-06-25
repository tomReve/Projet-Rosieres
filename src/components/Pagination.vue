<template>
    <div class="pagination">
        <div class="pagination-arrow" @click="toFirst">
            <img src="../assets/angle-double-left-solid.svg" alt="arrow-left" class="arrow-pagination"/>
        </div>
        <div class="pagination-link pagination-previous" :class="{'disabled':isDisabled('previous')}" @click="previousPage">{{ previous }}</div>
        <div class="pagination-selection">
            <div v-for="page in pagesToDisplay" :key="page" class="pagination-page-link" :class="{'selected':isSelected(page)}" @click="selectPage(page)">{{ page }}</div>
        </div>
        <div class="pagination-link pagination-next" @click="nextPage" :class="{'disabled':isDisabled('next')}">{{ next }}</div>
        <div class="pagination-arrow" @click="toLast">
            <img src="../assets/angle-double-right-solid.svg" alt="arrow-right" class="arrow-pagination"/>
        </div>
    </div>
</template>

<script>
export default {
    name:'Pagination',
    props:{
        page:{
            type:Number,
            default:1
        },
        pages:{
            type:Number,
            default:10
        },
        display:{
            type:Number,
            default: 5,
        },
        previous:{
            type:String,
            default: 'Previous'
        },
        next:{
            type: String,
            default: 'Next'
        },
    },
    data(){
        return {
            localPage:null
        }
    },
    computed:{
        pagesToDisplay(){/* Doit etre amélioré pour fonctionner avec tte les props */
            const pages = [];
            if(this.page == 1) {
                for(let i = this.page; i < this.page+this.display;i++) {
                    pages.push(i);
                }
            } else if(this.page == 2) {
                pages.push(1);
                for(let i = this.page; i < this.page+this.display-1;i++) {
                    pages.push(i);
                }
            } else if(this.page == this.pages - 1) {
                for(let i = this.page-(this.display-2); i <= this.page;i++) {
                    pages.push(i);
                }
                pages.push(this.pages);                
            } else if(this.page == this.pages) {
                for(let i = this.page-(this.display-1); i <= this.page;i++) {
                    pages.push(i);
                }                
            } else {
                for(let i = this.page-(this.display-Math.ceil(this.display/2)); i <= this.page+Math.floor(this.display/2);i++) {
                    pages.push(i);
                }              
            }
            return pages;
        }
    },
    watch:{
        page(page) {
            this.localPage = page;
        }
    },
    methods:{
        previousPage(){
            if(this.page > 1) {
                this.$emit('change',this.page-1);
            }
        },
        nextPage(){
            if(this.page < this.pages) {
                this.$emit('change',this.page+1);
            }
        },
        selectPage(page){
            this.$emit('change',page);
        },
        isSelected(page) {
            if(page == this.page) {
                return true;
            } else {
                return false;
            }
        },
        toFirst(){
            this.$emit('change',1);
        },
        toLast(){
            this.$emit('change',this.pages);
        },
        isDisabled(position){
            if(position == 'previous' && this.page == 1) {
                return true;
            } else if(position == 'next' && this.page == this.pages) {
                return true;
            } else {
                return false;
            }
        }
    }
}
</script>

<style lang="scss">

#pagination
{
    display: flex;
    justify-content: space-around;
}
#pagination-bottom {
    display: flex;
    width: 100%;
    justify-content: flex-end;
}
.pagination {
    width: min-content;
    display: flex;
    align-items: center;
    flex-direction: row;
    justify-content: space-around;
    .pagination-selection {
        width: 60%;
        display: flex;
        justify-content: space-around;
    }

    .pagination-link, .pagination-page-link, .pagination-arrow {
        cursor: pointer;
        margin: 0 .5em;
    }

    .pagination-arrow {
        display: flex;
        align-items: center;
    }

    .pagination-page-link.selected,
    .pagination-link
    {
        font-weight: 600;
    }

    .pagination-page-link.selected {
        font-weight: bolder;
        color: #FF8F00;
    }

    .pagination-link.disabled {
        color: #000000ab;
    }
    .arrow-pagination {
        width: 1em;
    }
}
</style>
