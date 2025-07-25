<template>
    <div v-if="design === itemDesignEnum.LIST" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-3 md:gap-6">
        <div v-for="item in items" :key="item" v-show="type === null || type === item.item_type"
            class="product-card-list">
            <img class="product-card-list-image" :src="item.thumb" alt="thumbnail">
            <div class="product-card-list-content-group">
                <div class="product-card-list-header-group">
                    <h3 class="product-card-list-title">{{ textShortener(item.name, 25) }}</h3>
                    <button class="product-card-list-info-btn info-btn" data-modal="#item-info-modal"
                        @click.prevent="infoModalShow(item.name, item.caution)">
                        <i class="lab lab-information font-fill-paragraph transition lab-font-size-16"></i>
                    </button>
                </div>
                <p class="product-card-list-describe char-limit">
                    {{ textShortener(item.description, 65) }}
                </p>
                <div class="product-card-list-footer-group">
                    <div class="product-card-list-price-group">
                        <del v-if="item.offer.length > 0" class="product-card-grid-price-previous">
                            {{ item.currency_price }}
                        </del>
                        <h4 class="product-card-list-price-current">
                            {{ item.offer.length > 0 ? item.offer[0].currency_price : item.currency_price }}
                        </h4>
                    </div>
                    <button @click.prevent="variationModalShow(item)" data-modal="#item-variation-modal"
                        class="product-card-list-cart-btn add-btn">
                        <i class="lab lab-bag-2 font-fill-primary transition lab-font-size-14"></i>
                        <span class="text-xs text-primary transition">{{ $t('button.add') }}</span>
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div v-else-if="design === itemDesignEnum.GRID"
        class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-3 lg:gap-6">
        <div v-for="item in items" :key="item" v-show="type === null || type === item.item_type"
            class="product-card-grid">
            <img class="product-card-grid-image" :src="item.cover" alt="product">
            <div class="product-card-grid-content-group">
                <div class="product-card-grid-header-group">
                    <h3 class="product-card-grid-title">{{ textShortener(item.name, 26) }}</h3>
                    <button type="button" class="product-card-grid-info-btn info-btn" data-modal="#item-info-modal"
                        @click.prevent="infoModalShow(item.name, item.caution)">
                        <i class="lab lab-information font-fill-paragraph transition lab-font-size-16"></i>
                    </button>
                </div>
                <p class="product-card-grid-describe char-limit">{{ textShortener(item.description, 75) }}</p>
                <div class="product-card-grid-footer-group">
                    <div class="product-card-grid-price-group">
                        <del v-if="item.offer.length > 0" class="product-card-grid-price-previous">
                            {{ item.currency_price }}
                        </del>
                        <h4 class="product-card-grid-price-current">
                            {{ item.offer.length > 0 ? item.offer[0].currency_price : item.currency_price }}
                        </h4>
                    </div>
                    <button @click.prevent="variationModalShow(item)" data-modal="#item-variation-modal"
                        class="product-card-grid-cart-btn add-btn">
                        <i class="lab lab-bag-2 font-fill-primary transition lab-font-size-14"></i>
                        <span class="text-xs text-primary transition">{{ $t('button.add') }}</span>
                    </button>
                </div>
            </div>
        </div>
    </div>



    <div id="item-info-modal" ref="itemInfoModal" class="modal ff-modal info-modal">
        <div class="modal-dialog" v-if="itemInfo">
            <div class="modal-header">
                <h3 class="modal-title text-base font-medium">{{ itemInfo.name }}</h3>
                <button class="modal-close fa-regular fa-circle-xmark" @click.prevent="infoModalHide"></button>
            </div>
            <div class="modal-body">
                {{ itemInfo.caution }}
            </div>
        </div>
    </div>

    <div id="item-variation-modal" ref="itemVariationModal" class="modal ff-modal">
        <div class="modal-dialog max-w-[647px]" v-if="item">
            <div class="modal-header items-start border-none pb-0">
                <div class="flex gap-4">
                    <img class="flex-shrink-0 w-[72px] h-[72px] object-cover rounded-lg" :src="item.thumb"
                        alt="thumbnail">
                    <div class="flex-auto">
                        <div class="flex items-start gap-2 mb-1">
                            <h3 class="text-sm font-semibold capitalize">{{ item.name }}</h3>
                            <button type="button" class="info-btn mt-0.5" data-modal="#item-info-modal"
                                @click.prevent="infoModalShow(item.name, item.caution)">
                                <i class="lab lab-information font-fill-paragraph transition lab-font-size-16"></i>
                            </button>
                        </div>
                        <p class="text-xs mb-2">{{ item.description }}</p>
                        <h4 class="text-sm font-semibold">
                            {{ item.offer.length > 0 ? item.offer[0].currency_price : item.currency_price }}</h4>
                    </div>
                </div>
                <button class="modal-close lab-close-circle-line font-fill-danger lab-font-size-24"
                    @click.prevent="variationModalHide"></button>
            </div>
            <div class="modal-body">
                <div class="flex items-center gap-2 mb-4">
                    <h3 class="text-sm leading-6 font-medium first-letter:uppercase text-heading">
                        {{ $t('label.quantity') }}:</h3>
                    <div class="flex items-center indec-group py-1 px-2 rounded-xl bg-[#F7F7FC]">
                        <button @click.prevent="quantityDecrement"
                            class="fa-solid fa-minus text-[10px] w-[18px] h-[18px] leading-4 text-center rounded-full border transition text-primary border-primary hover:bg-primary hover:text-white indec-minus"></button>
                        <input type="number" v-on:keypress="onlyNumber($event)" v-on:keyup="quantityUp"
                            v-model="temp.quantity"
                            class="text-center w-7 text-xs font-semibold text-heading indec-value">
                        <button @click.prevent="quantityIncrement"
                            class="fa-solid fa-plus text-[10px] w-[18px] h-[18px] leading4 text-center rounded-full border transition text-primary border-primary hover:bg-primary hover:text-white indec-plus"></button>
                    </div>
                </div>
                <div class="mb-4" v-if="item.itemAttributes.length > 1">
                    <div class="row">
                        <div v-for="itemAttribute in item.itemAttributes" class="col-12 sm:col-6">
                            <label class="text-sm leading-6 block font-medium capitalize mb-1.5 text-heading">
                                {{ itemAttribute.name }}
                            </label>
                            <div class="relative">
                                <i
                                    class="lab lab-arrow-down text-sm absolute top-1/2 ltr:right-2.5 rtl:left-2.5 -translate-y-1/2 lab-font-size-16"></i>
                                <select
                                    @change.prevent="changeVariationAdjust(itemAttribute.id, temp.item_variations.variations[itemAttribute.id])"
                                    v-model="temp.item_variations.variations[itemAttribute.id]"
                                    class="text-xs capitalize rounded-lg h-10 w-full py-1.5 px-2.5 appearance-none transition border border-[#EFF0F6] text-heading hover:border-primary/30">
                                    <option :value="variation.id" v-for="variation in item.variations[itemAttribute.id]"
                                        :key="variation">{{ variation.name }} +{{ variation.currency_price }}
                                    </option>
                                </select>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="mb-4" v-else-if="item.itemAttributes.length > 0">
                    <h3 class="text-sm leading-6 font-medium capitalize mb-2 text-heading">
                        {{ item.itemAttributes[0].name }}
                    </h3>
                    <div class="swiper size-swiper">
                        <div class="size-tabs">
                            <Swiper :speed="1000" slidesPerView="auto" :spaceBetween="16">
                                <SwiperSlide v-for="variation in item.variations[item.itemAttributes[0].id]"
                                    :key="variation" class="!w-fit !relative">
                                    <label
                                        :class="temp.item_variations.variations[variation.item_attribute_id] === variation.id ? 'active' : ''"
                                        :for="variation.item_attribute_id + '-' + variation.name"
                                        class="w-full min-w-[120px] h-[52px] cursor-pointer py-2 px-3 gap-2 rounded-lg flex items-center border transition border-[#F7F7FC] bg-[#F7F7FC]">
                                        <div class="custom-radio sm">
                                            <input :value="variation.id"
                                                @click="changeVariation(variation.item_attribute_id, variation.id, variation.name, variation.convert_price)"
                                                v-model="temp.item_variations.variations[variation.item_attribute_id]"
                                                type="radio" :id="variation.item_attribute_id + '-' + variation.name"
                                                class="custom-radio-field">
                                            <span class="custom-radio-span"></span>
                                        </div>
                                        <div class="ltr:text-left rtl:text-right">
                                            <h3 class="block capitalize text-xs text-heading">
                                                {{ textShortener(variation.name, 15) }}</h3>
                                            <h4 v-if="variation.price > 0"
                                                class="block text-xs font-medium text-heading">
                                                +{{ variation.currency_price }}
                                            </h4>
                                        </div>
                                    </label>
                                </SwiperSlide>
                            </Swiper>
                        </div>
                    </div>
                </div>

                <div class="mb-4" v-if="item.extras.length > 0">
                    <h3 class="text-sm leading-6 font-medium capitalize mb-2 text-heading">{{ $t('label.extras') }}</h3>
                    <div class="extra-swiper">
                        <div class="">
                            <Swiper :speed="1000" slidesPerView="auto" :spaceBetween="16">
                                <SwiperSlide v-for="extra in item.extras" :key="extra" class="!w-fit !relative">
                                    <label :for="extra.id + extra.name"
                                        class="extra h-[52px] min-w-[120px] cursor-pointer py-2 px-3 gap-3 rounded-lg flex items-center border transition border-[#F7F7FC] bg-[#F7F7FC]">
                                        <div class="custom-checkbox w-3 h-3">
                                            <input :id="extra.id + extra.name"
                                                @change.prevent="changeExtra($event, extra.id, extra.name)"
                                                :value="extra.id" type="checkbox" class="custom-checkbox-field">
                                            <i
                                                class="fa-solid fa-check custom-checkbox-icon leading-[9px] text-[9px] rounded-[3px]"></i>
                                        </div>
                                        <div>
                                            <h3 class="block capitalize mb-1 text-xs text-heading">
                                                {{ textShortener(extra.name, 15) }}</h3>
                                            <h4 class="block text-xs font-medium text-heading">+{{
                                                extra.currency_price
                                            }}</h4>
                                        </div>
                                    </label>
                                </SwiperSlide>
                            </Swiper>
                        </div>
                    </div>
                </div>

                <div class="mb-5" v-if="item.addons.length > 0">
                    <h3 class="text-sm leading-6 font-medium capitalize mb-2 text-heading">{{ $t('label.addons') }}</h3>
                    <div class="swiper addon-swiper">
                        <Swiper :speed="1000" slidesPerView="auto" :spaceBetween="16">
                            <SwiperSlide v-for="addon in item.addons" :key="addon" class="!w-fit !relative">
                                <div class="swiper-slide">
                                    <div @click.prevent="changeAddon(addon)"
                                        class="addon cursor-pointer w-fit min-w-[200px] h-[70px] rounded-lg flex border border-[#EFF0F6]">
                                        <img class="w-[68px] h-full object-cover ltr:rounded-l-lg rtl:rounded-r-lg flex-shrink-0"
                                            :src="addon.thumb" alt="thumbnail">
                                        <div class="rounded-r-lg w-full py-1 px-2">
                                            <span
                                                class="block text-xs text-ellipsis whitespace-nowrap overflow-hidden w-fit max-w-[100px] capitalize text-heading">
                                                {{ addon.addon_item_name }}
                                            </span>
                                            <p v-if="addon.variation_names.length > 0"
                                                class="text-left text-[10px] leading-4 capitalize mb-1.5 cursor-pointer">
                                                <span v-for="(variation, index) in addon.variation_names">
                                                    {{ textShortener(variation.name, 8) }}
                                                    <span v-if="index + 1 < addon.variation_names">, </span> &nbsp;
                                                </span>
                                            </p>
                                            <span
                                                class="block text-xs font-semibold text-heading ltr:text-left rtl:text-right">
                                                {{ addon.total_currency_price }}
                                            </span>
                                        </div>
                                    </div>
                                    <div
                                        class="flex flex-col items-end justify-between h-full absolute top-0 ltr:right-0 rtl:left-0 z-10 p-2">
                                        <button type="button" class="info-btn" data-modal="#item-info-modal"
                                            @click.prevent="infoModalShow(addon.addon_item_name, addon.caution)">
                                            <i
                                                class="lab lab-information font-fill-paragraph transition lab-font-size-16"></i>
                                        </button>

                                        <div class="flex items-center indec-group">
                                            <button @click.prevent="addonQuantityDecrement(addon.id)"
                                                class="fa-solid fa-minus text-[8px] w-4 h-4 leading-3 text-center rounded-full border transition text-primary border-primary hover:bg-primary hover:text-white indec-minus"></button>
                                            <input v-on:keypress="onlyNumber($event)"
                                                v-on:keyup="addonQuantityUp(addon.id)" v-model="addonQuantity[addon.id]"
                                                type="number"
                                                class="text-center w-5 text-xs font-semibold text-heading indec-value">
                                            <button @click.prevent="addonQuantityIncrement(addon.id)"
                                                class="fa-solid fa-plus text-[8px] w-4 h-4 leading-3 text-center rounded-full border transition text-primary border-primary hover:bg-primary hover:text-white indec-plus"></button>
                                        </div>
                                    </div>
                                </div>
                            </SwiperSlide>
                        </Swiper>
                    </div>
                </div>

                <div class="mb-6">
                    <h3 class="text-xs leading-6 font-medium capitalize mb-2 text-heading">
                        {{ $t('label.special_instructions') }}
                    </h3>
                    <textarea v-model="temp.instruction" :placeholder="$t('message.add_note')"
                        class="h-12 w-full rounded-lg border py-1.5 px-2 placeholder:text-[10px] placeholder:text-[#6E7191] border-[#D9DBE9]"></textarea>
                </div>
                <button type="button" :disabled="temp.total_price <= 0" @click.prevent="addToCart"
                    class="flex items-center justify-center gap-3 rounded-3xl text-base py-3 px-3 font-medium w-full text-white bg-primary">
                    <i class="icon-bag-2"></i>
                    <span>
                        {{ $t('button.add_to_cart') }} -
                        {{
                            currencyFormat(temp.total_price, setting.site_digit_after_decimal_point,
                                setting.site_default_currency_symbol, setting.site_currency_position)
                        }}
                    </span>
                </button>
            </div>
        </div>
    </div>
</template>
<script>
import itemDesignEnum from "../../../enums/modules/itemDesignEnum";
import appService from "../../../services/appService";
import _ from 'lodash';
import alertService from "../../../services/alertService";
import { Swiper, SwiperSlide } from 'swiper/vue';
import 'swiper/css';


export default {
    name: "ItemComponent",
    components: {
        Swiper,
        SwiperSlide,
    },
    props: {
        items: Object,
        design: Number,
        type: Number
    },
    data() {
        return {
            item: null,
            itemInfo: null,
            addons: {},
            addonQuantity: {},
            itemArrays: [],
            itemDesignEnum: itemDesignEnum,
            settings: {
                itemsToShow: 4.3,
                wrapAround: false,
                snapAlign: "start"
            },
            addonSettings: {
                itemsToShow: 3,
                wrapAround: false,
                snapAlign: "start"
            },
            temp: {
                name: "",
                image: "",
                item_id: 0,
                quantity: 0,
                discount: 0,
                currency_price: 0,
                convert_price: 0,
                item_variations: {
                    variations: {},
                    names: {}
                },
                item_extras: {
                    extras: [],
                    names: []
                },
                item_variation_total: 0,
                item_extra_total: 0,
                total_price: 0,
                instruction: "",
            },
        }
    },
    computed: {
        setting: function () {
            return this.$store.getters['frontendSetting/lists'];
        },
    },
    methods: {
        setup() {
            const spaceBetween = 10;
            const onProgress = (e) => {
                const [swiper, progress] = e.detail;
            };

            const onSlideChange = (e) => {

            }

            return {
                spaceBetween,
                onProgress,
                onSlideChange,
            };
        },
        onlyNumber: function (e) {
            return appService.onlyNumber(e);
        },
        textShortener: function (text, number) {
            return appService.textShortener(text, number);
        },
        currencyFormat: function (amount, decimal, currency, position) {
            return appService.currencyFormat(amount, decimal, currency, position);
        },
        infoModalShow: function (name, caution) {
            this.itemInfo = {
                name: name,
                caution: caution
            };
            const modalTarget = this.$refs.itemInfoModal;
            modalTarget?.classList?.add("active");
            document.body.style.overflowY = "hidden";
        },
        infoModalHide: function () {
            this.itemInfo = null;
            const modalDiv = this.$refs.itemInfoModal;
            modalDiv?.classList?.remove("active");
            document.body.style.overflowY = "auto";
        },
        variationModalShow: function (selectedItem) {

            this.$store.dispatch('frontendItem/details', selectedItem.id)
                .then((res) => {

                    const item = res.data.data;
                    this.item = res.data.data;

                    if (this.item.itemAttributes.length > 0) {
                        _.forEach(this.item.itemAttributes, (element) => {
                            if (typeof this.item.variations[element.id][0] !== "undefined") {
                                this.temp.item_variations.variations[this.item.variations[element.id][0].item_attribute_id] = this.item.variations[element.id][0].id;
                                this.temp.item_variations.names[element.name] = this.item.variations[element.id][0].name;
                                this.temp.item_variation_total += this.item.variations[element.id][0].convert_price;
                            }
                        });
                    }

                    if (this.item.addons.length > 0) {
                        _.forEach(this.item.addons, (addon) => {
                            this.addonQuantity[addon.id] = 1;
                        });
                    }

                    this.temp.name = this.item.name;
                    this.temp.image = this.item.thumb;
                    this.temp.item_id = this.item.id;
                    this.temp.quantity = 1;
                    this.temp.discount = 0;
                    this.temp.convert_price = item.offer.length > 0 ? item.offer[0].convert_price : item.convert_price;
                    this.temp.currency_price = item.offer.length > 0 ? item.offer[0].currency_price : item.currency_price;
                    this.temp.total_price = (item.offer.length > 0 ? item.offer[0].convert_price : item.convert_price) + this.temp.item_variation_total;

                    const modalTarget = this.$refs.itemVariationModal;
                    modalTarget?.classList?.add("active");
                    document.body.style.overflowY = "hidden";

                }).catch({});
        },
        variationModalHide: function () {
            this.item = null;

            this.temp.name = "";
            this.temp.image = "";
            this.temp.item_id = 0;
            this.temp.quantity = 0;
            this.temp.discount = 0;
            this.temp.currency_price = 0;
            this.temp.convert_price = 0;
            this.temp.item_variations = {
                variations: {},
                names: {}
            };
            this.temp.item_extras = {
                extras: [],
                names: []
            };
            this.temp.item_variation_total = 0;
            this.temp.item_extra_total = 0;
            this.temp.total_price = 0;
            this.temp.instruction = "";
            this.addons = {};

            const modalDiv = this.$refs.itemVariationModal;
            modalDiv?.classList?.remove("active");
            document.body.style.overflowY = "auto";
        },
        changeVariation: function (attributeId, variationId, variationName, variationPrice) {
            this.temp.item_variations.variations[attributeId] = variationId;
            _.forEach(this.item.itemAttributes, (element) => {
                if (element.id === attributeId) {
                    this.temp.item_variations.names[element.name] = variationName;
                }
            });
            this.totalPriceSetup();
        },
        changeVariationAdjust: function (attributeId, variationId) {
            _.forEach(this.item.variations[attributeId], (variation) => {
                if (variation.id === variationId) {
                    this.changeVariation(attributeId, variationId, variation.name, variation.convert_price);
                }
            });
        },
        changeExtra: function (e, id, name) {
            if (e.target.checked) {
                this.temp.item_extras.extras.push(id);
                this.temp.item_extras.names.push(name);
            } else {
                for (let i = 0; i < this.temp.item_extras.extras.length; i++) {
                    if (this.temp.item_extras.extras[i] === id) {
                        this.temp.item_extras.extras.splice(i, 1);
                    }
                }
                for (let i = 0; i < this.temp.item_extras.names.length; i++) {
                    if (this.temp.item_extras.names[i] === name) {
                        this.temp.item_extras.names.splice(i, 1);
                    }
                }
            }
            this.totalPriceSetup();
        },
        totalPriceSetup: function () {
            let item_variation_total = 0;
            let item_extra_total = 0;
            let item_addon_total = 0;
            _.forEach(this.temp.item_variations.variations, (variationId, attributeId) => {
                _.forEach(this.item.variations[attributeId], (itemVariation) => {
                    if (variationId === itemVariation.id) {
                        item_variation_total += itemVariation.convert_price;
                    }
                });
            });

            _.forEach(this.temp.item_extras.extras, (extraId) => {
                _.forEach(this.item.extras, (itemExtra) => {
                    if (extraId === itemExtra.id) {
                        item_extra_total += itemExtra.convert_price;
                    }
                });
            });

            _.forEach(this.addons, (addon) => {
                item_addon_total += (addon.total_price * addon.quantity);
            });

            this.temp.item_variation_total = item_variation_total;
            this.temp.item_extra_total = item_extra_total;
            this.temp.total_price = parseFloat((((this.item.offer.length > 0 ? this.item.offer[0].convert_price : this.item.convert_price) + this.temp.item_variation_total + this.temp.item_extra_total) * this.temp.quantity) + item_addon_total);
        },
        quantityUp: function () {
            if (this.temp.quantity === 0) {
                this.temp.quantity = 1;
            }
            this.totalPriceSetup();
        },
        quantityIncrement: function () {
            this.temp.quantity++;
            if (this.temp.quantity <= 0) {
                this.temp.quantity = 1;
            }
            this.totalPriceSetup();
        },
        quantityDecrement: function () {
            this.temp.quantity--;
            if (this.temp.quantity <= 0) {
                this.temp.quantity = 1;
            }
            this.totalPriceSetup();
        },
        addonQuantityUp: function (id) {
            if (typeof this.addonQuantity[id] !== "undefined") {
                if (this.addonQuantity[id] === 0) {
                    this.addonQuantity[id] = 1;
                }
            }
            if (typeof this.addons[id] !== "undefined") {
                this.addons[id].quantity = this.addonQuantity[id];
            }

            this.totalPriceSetup();
        },
        addonQuantityIncrement: function (id) {
            if (typeof this.addonQuantity[id] !== "undefined") {
                this.addonQuantity[id]++;
                if (this.addonQuantity[id] <= 0) {
                    this.addonQuantity[id] = 1;
                }
                if (typeof this.addons[id] !== "undefined") {
                    this.addons[id].quantity = this.addonQuantity[id];
                }
                this.totalPriceSetup();
            }
        },
        addonQuantityDecrement: function (id) {
            if (typeof this.addonQuantity[id] !== "undefined") {
                this.addonQuantity[id]--;
                if (this.addonQuantity[id] <= 0) {
                    this.addonQuantity[id] = 1;
                }
                if (typeof this.addons[id] !== "undefined") {
                    this.addons[id].quantity = this.addonQuantity[id];
                }
                this.totalPriceSetup();
            }
        },
        changeAddon: function (addon) {
            if (typeof this.addons[addon.id] === "undefined") {
                this.addons[addon.id] = {
                    name: addon.addon_item_name,
                    image: addon.thumb,
                    item_id: addon.item_addon_id,
                    quantity: this.addonQuantity[addon.id],
                    discount: 0,
                    currency_price: addon.offer.length > 0 ? addon.offer[0].currency_price : addon.addon_item_currency_price,
                    convert_price: addon.offer.length > 0 ? addon.offer[0].convert_price : addon.addon_item_convert_price,
                    item_variations: {
                        variations: {},
                        names: {}
                    },
                    item_extras: {
                        extras: [],
                        names: []
                    },
                    item_variation_total: addon.variation_total_convert_price,
                    item_extra_total: 0,
                    total_price: addon.total_convert_price,
                    instruction: "",
                };
                if (addon.variations !== "undefined" && Object.keys(addon.variations).length !== 0) {
                    _.forEach(addon.variations, (variationId, attributeId) => {
                        this.addons[addon.id].item_variations.variations[attributeId] = variationId;
                    });

                }
                if (addon.variation_names.length > 0) {
                    _.forEach(addon.variation_names, (variation) => {
                        this.addons[addon.id].item_variations.names[variation.attribute_name] = variation.name;
                    });
                }
            } else {
                delete this.addons[addon.id];
            }
            this.totalPriceSetup();
        },
        addToCart: function () {
            this.itemArrays = [
                {
                    name: this.temp.name,
                    image: this.temp.image,
                    item_id: this.temp.item_id,
                    quantity: this.temp.quantity,
                    discount: this.temp.discount,
                    currency_price: this.temp.currency_price,
                    convert_price: this.temp.convert_price,
                    item_variations: this.temp.item_variations,
                    item_extras: this.temp.item_extras,
                    item_variation_total: this.temp.item_variation_total,
                    item_extra_total: this.temp.item_extra_total,
                    instruction: this.temp.instruction
                }
            ];

            if (this.addons !== "undefined" && Object.keys(this.addons).length !== 0) {
                _.forEach(this.addons, (addon) => {
                    this.itemArrays.push({
                        name: addon.name,
                        image: addon.image,
                        item_id: addon.item_id,
                        quantity: addon.quantity,
                        discount: addon.discount,
                        price: addon.price,
                        currency_price: addon.currency_price,
                        convert_price: addon.convert_price,
                        item_variations: addon.item_variations,
                        item_extras: addon.item_extras,
                        item_variation_total: addon.item_variation_total,
                        item_extra_total: addon.item_extra_total,
                        instruction: addon.instruction
                    });
                });
            }

            if (this.itemArrays.length > 0) {
                this.$store.dispatch("frontendCart/lists", this.itemArrays).then((res) => {
                    this.item = null;
                    this.temp.name = "";
                    this.temp.image = "";
                    this.temp.item_id = 0;
                    this.temp.quantity = 0;
                    this.temp.discount = 0;
                    this.temp.currency_price = 0;
                    this.temp.convert_price = 0;
                    this.temp.item_variations = {
                        variations: {},
                        names: {}
                    };
                    this.temp.item_extras = {
                        extras: [],
                        names: []
                    };
                    this.temp.item_variation_total = 0;
                    this.temp.item_extra_total = 0;
                    this.temp.total_price = 0;
                    this.temp.instruction = "";
                    this.addons = {};
                    this.itemArrays = [];

                    alertService.success(this.$t('message.add_to_cart'));
                    appService.modalHide('#item-variation-modal');
                }).catch();
            }
        },
    }
}
</script>
<style>
.swiper-variation .swiper-wrapper {
    gap: 16px;
}
</style>