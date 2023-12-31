<script setup>
//* LIBRARY
import { computed, ref } from 'vue';

//* HELPER
import { useDispatch, useSelector } from '../../helpers';

//* PROVIDER
import {
  decrementQuantity,
  deleteToCart,
  incrementQuantity,
} from '../../providers/redux/cart/cart_thunk';

//* UTILS
import { getImage, rouserNumber } from '../../utils/index';

//* COMPONENTS
import OrderConfirmation from '../../components/Confirm/Confirmation.vue';
import EmptyCartVue from '../../components/Empty/EmptyCart.vue';

const dispatch = useDispatch();

const showConfirmation = ref(false);

const storeCart = useSelector((state) => state.carts);

const noteValue = ref('');

// Handle delete cart
const handleDeleteCart = (id) => {
  dispatch(deleteToCart({ productId: id }));
};

// Handle increment quantity
const handleIncrementQuantity = (id) => {
  dispatch(incrementQuantity({ productId: id }));
};

// Handle increment quantity
const handleDecrementQuantity = (id) => {
  dispatch(decrementQuantity({ productId: id }));
};

// Show order confirmation
const showOrderConfirmation = () => {
  showConfirmation.value = true;
};

// Hide order confirmation
const hideOrderConfirmation = () => {
  showConfirmation.value = false;
};

const isLimitLengthNote = computed(() => noteValue.value.length > 50);
</script>

<template>
  {{ console.log(isLimitLengthNote, '----1------') }}
  <!-- Content Cart -->
  <section class="wrap-cart">
    <div class="w-full">
      <div class="container mx-auto">
        <!-- No data cart -->
        <EmptyCartVue v-if="storeCart.cart.length === 0" />

        <div v-else>
          <!-- Cart exits -->
          <div class="relative w-full overflow-x-auto border border-[#ededed]">
            <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
              <tbody>
                <tr
                  class="text-[13px] font-medium text-black bg-[#f6f6f6] whitespace-nowrap px-2 border-b default-border-bottom uppercase"
                >
                  <td class="py-4 pl-10 whitespace-nowrap min-w-[300px]">Product</td>
                  <td class="py-4 whitespace-nowrap min-w-[200px] text-center">Price</td>
                  <td class="py-4 whitespace-nowrap min-w-[200px] text-center">Discounted price</td>
                  <td class="py-4 whitespace-nowrap min-w-[300px] text-center">Quantity</td>
                  <td class="py-4 whitespace-nowrap min-w-[300px] text-center">Total</td>
                  <td class="py-4 whitespace-nowrap text-right w-[114px]"></td>
                </tr>

                <!-- Data -->
                <tr
                  v-for="cart in storeCart.cart"
                  :key="cart.id"
                  class="bg-white border-b hover:bg-gray-50"
                >
                  <td class="pl-10 py-4 w-[380px]">
                    <div class="flex space-x-6 items-center">
                      <div
                        class="w-[80px] h-[80px] overflow-hidden flex justify-center items-center border border-[#ededed] relative"
                      >
                        <img
                          :src="getImage(cart.image_url)"
                          alt="product"
                          class="w-full h-full object-contain"
                        />
                      </div>
                      <div class="flex-1 flex flex-col">
                        <RouterLink :to="`/product/${cart.id}`">
                          <p class="font-medium text-[15px] text-black-500">
                            {{ cart.name }}
                          </p>
                        </RouterLink>
                      </div>
                    </div>
                  </td>

                  <td class="text-center py-4 px-2">
                    <div class="flex space-x-1 items-center justify-center">
                      <span
                        :class="cart.discounted_price ? 'line-through ' : ''"
                        class="text-[15px] font-normal"
                        >$ {{ cart.original_price }}</span
                      >
                    </div>
                  </td>

                  <td class="text-center py-4 px-2">
                    <div class="flex space-x-1 items-center justify-center">
                      <span
                        :class="cart.discounted_price ? 'text-red-500' : ''"
                        class="text-[15px] font-normal"
                        >$ {{ cart.discounted_price || cart.original_price }}</span
                      >
                    </div>
                  </td>

                  <td class="text-center py-4 px-2">
                    <div class="flex justify-center items-center">
                      <div
                        class="w-[120px] h-[40px] px-[26px] flex items-center border border-gray-300"
                      >
                        <div class="flex justify-between items-center w-full">
                          <button
                            class="text-base text-gray-500"
                            @click="handleDecrementQuantity(cart.id)"
                          >
                            -
                          </button>
                          <span>{{ cart.quantity }}</span>
                          <button
                            class="text-base text-gray-500"
                            @click="handleIncrementQuantity(cart.id)"
                          >
                            +
                          </button>
                        </div>
                      </div>
                    </div>
                  </td>

                  <td class="text-right py-4 px-2">
                    <div class="flex space-x-1 items-center justify-center">
                      <span class="text-[15px] font-normal"
                        >${{
                          rouserNumber(
                            (cart.discounted_price || cart.original_price) * cart.quantity
                          )
                        }}</span
                      >
                    </div>
                  </td>

                  <td class="text-right py-4">
                    <div
                      class="flex space-x-1 items-center justify-center"
                      @click="handleDeleteCart(cart.id)"
                    >
                      <span
                        class="cursor-pointer flex p-4 rounded-full tems-center justify-center hover:bg-gray-200"
                      >
                        <i class="fa-solid fa-xmark"></i>
                      </span>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Info Paying -->
          <div class="w-full mt-[23px]">
            <div class="container mx-auto">
              <div class="w-full sm:flex justify-between mb-10">
                <div class="sm:w-[570px] w-full mb-5 sm:mb-0 h-[50px] flex">
                  <div class="flex-1 h-full">
                    <label
                      for="message"
                      class="block mb-2 text-lg font-medium text-gray-900 dark:text-white"
                      >Note</label
                    >
                    <textarea
                      id="message"
                      v-model="noteValue"
                      rows="5"
                      class="border p-2.5 placeholder:text-lg text-lg px-6 text-dark-gray w-full h-40 rounded-lg font-normal bg-white focus:outline-none"
                      :class="
                        isLimitLengthNote
                          ? 'border-2 border-red-500 animate-snake'
                          : 'focus:border-yellow-500'
                      "
                      placeholder="Write your thoughts here..."
                    ></textarea>
                  </div>
                </div>
              </div>

              <div class="w-full mt-[30px] flex sm:justify-end">
                <div class="sm:w-[370px] w-full border border-[#ededed] px-[30px] py-[26px]">
                  <div class="mb-6">
                    <div class="flex justify-between mb-6">
                      <p class="text-[15px] font-medium text-black">Cost Total</p>
                      <p class="text-[15px] font-medium text-red-500">
                        ${{ rouserNumber(storeCart.cost) }}
                      </p>
                    </div>
                    <div class="w-full h-[1px] bg-[#ededed]"></div>
                  </div>

                  <div class="w-full mb-3">
                    <div class="mb-[17px]">
                      <h1 class="text-[15px] font-medium">Calculate Shipping</h1>
                    </div>
                    <div
                      class="w-full h-[50px] border border-[#EDEDED] px-5 flex justify-between items-center mb-10"
                    >
                      <input
                        type="text"
                        placeholder="Enter Address..."
                        class="input-field placeholder:text-sm text-sm px-6 text-dark-gray w-full h-full font-normal bg-white focus:ring-0 focus:outline-none w-full h-full"
                      />
                    </div>
                    <div class="mb-6">
                      <div class="flex justify-between">
                        <p class="text-[18px] font-medium text-black">Total</p>
                        <p class="text-[18px] font-medium text-red-500">
                          ${{ rouserNumber(storeCart.total) }}
                        </p>
                      </div>
                    </div>
                  </div>

                  <div class="w-full h-[50px] bg-black text-white flex justify-center items-center">
                    <button class="text-sm font-semibold" @click="showOrderConfirmation">
                      Proceed to Checkout
                    </button>
                    <Teleport to="body">
                      <OrderConfirmation
                        :show-confirmation="showConfirmation"
                        :hide-order-confirmation="hideOrderConfirmation"
                      />
                    </Teleport>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
