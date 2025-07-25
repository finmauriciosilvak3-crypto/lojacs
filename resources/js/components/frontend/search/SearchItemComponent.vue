<template>
    <LoadingComponent :props="loading" />
    <section class="mb-16 mt-8">
        <div class="container">
            <div class="flex gap-4 flex-col sm:flex-row items-center justify-between mb-6">
                <h2 class="capitalize text-[26px] leading-[40px] font-semibold text-center sm:text-left text-primary">
                    {{ props.search.name }}
                </h2>
                <div class="flex items-center gap-3" v-if="props.search.name">
                    <button type="button" class="lab lab-row-vertical lab-font-size-20 text-xl"
                        v-on:click="itemProps.design = itemDesignEnum.LIST"
                        :class="itemProps.design === itemDesignEnum.LIST ? 'text-primary' : 'text-[#A0A3BD]'"></button>
                    <button type="button" class="lab lab-element-3 lab-font-size-20 text-xl"
                        v-on:click="itemProps.design = itemDesignEnum.GRID"
                        :class="itemProps.design === itemDesignEnum.GRID ? 'text-primary' : 'text-[#A0A3BD]'"></button>
                </div>
            </div>
            <ItemComponent :items="items" :type="itemProps.type" :design="itemProps.design" v-if="items.length > 0" />

            <div class="mt-12" v-else>
                <div class="max-w-[250px] mx-auto">
                    <img class="w-full mb-8" :src="setting.item_not_found" alt="image_order_not_found">
                </div>
                <span class="w-full mb-4 text-center text-black">{{ $t('message.no_items_found') }}</span>
                <router-link :to="{ name: 'frontend.home' }"
                    class="block w-full mx-auto max-w-[250px] py-3 rounded-3xl capitalize text-base font-medium leading-6 text-center bg-primary text-white">
                    {{ $t('button.go_home') }}
                </router-link>
            </div>
        </div>
    </section>
</template>

<script>
import ItemComponent from "../components/ItemComponent";
import itemDesignEnum from "../../../enums/modules/itemDesignEnum";
import statusEnum from "../../../enums/modules/statusEnum";
import alertService from "../../../services/alertService";
import LoadingComponent from "../components/LoadingComponent";

export default {
    name: "SearchItemComponent",
    components: {
        ItemComponent, LoadingComponent
    },
    data() {
        return {
            loading: {
                isActive: false,
            },
            itemDesignEnum: itemDesignEnum,
            items: {},
            itemProps: {
                design: itemDesignEnum.LIST,
                type: null,
            },
            props: {
                search: {
                    paginate: 0,
                    order_column: 'id',
                    order_type: 'asc',
                    name: "",
                    status: statusEnum.ACTIVE,
                }
            },
        };
    },
    mounted() {
        if (typeof this.$route.query.s !== "undefined" && this.$route.query.s !== "") {
            this.props.search.name = this.$route.query.s;
            this.loading.isActive = true;
            this.$store.dispatch("frontendItem/lists", this.props.search).then((res) => {
                this.items = res.data.data;
                this.loading.isActive = false;
            }).catch((err) => {
                this.loading.isActive = false;
            });
        }
    },
    computed: {
        setting: function () {
            return this.$store.getters['frontendSetting/lists'];
        }
    },
    methods: {
        itemTypeSet: function (e) {
            this.itemProps.type = e;
        },
        itemTypeReset: function () {
            this.itemProps.type = null;
        },
        searItems: function () {
            if (typeof this.$route.query.s !== "undefined" && this.$route.query.s !== "") {
                this.props.search.name = this.$route.query.s;
                this.loading.isActive = true;
                this.$store.dispatch("frontendItem/lists", this.props.search).then((res) => {
                    this.items = res.data.data;
                    this.loading.isActive = false;
                }).catch((err) => {
                    this.loading.isActive = false;
                    alertService.error(err.response.data.message);
                });
            }
        }

    },
    watch: {
        $route() {
            this.searItems();
        }
    }
};
</script>