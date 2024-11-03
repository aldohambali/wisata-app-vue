<template>
    <v-container max-width="1000" id="stay" v-if="!notFound">
      <v-row>

        <v-col cols="12">
  
          <div id="location-catalog" class="pt-0 pt-sm-4 pb-6 pb-sm-8">
            <v-row>

              <v-col cols="4" id="hero-image" class="d-flex justify-center">
                <div
                  class="v-avatar"
                  style="height: 168px; min-width: 168px; width: 168px"
                >
                  <div
                    class="bg-transparent overflow-hidden v-sheet theme--light aspect-ratio"
                    style="height: 0px; width: 100%; padding-bottom: 100%"
                  >
                  <v-avatar 
                  :image="hotelAvatar"
                  alt="Fairmont Jakarta"
                  style="height: 168px; min-width: 168px; width: 168px;"
                  fluid 
                  >
                  </v-avatar>
  
                  </div>
                </div>
              </v-col>
              <v-col cols="8" id="catalog-data" class="flex-grow-1 ml-n2 ml-sm-0 col">
                <div id="catalog-header-desktop">
                  <div class="align-baseline">
                    <h1 class="d-inline-block font-weight-medium pr-1 text-h4 me-1">
                      {{ hotel.name }}
                    </h1>
                    <span class="d-inline-flex flex-wrap">
                      <div style="margin-left: -3px">

                        

                        <span
                          v-for="star in rating"
                          :key="star"
                          class="star"
                        >
                          <span
                            aria-hidden="true"
                            class="v-icon notranslate mb-1 theme--light orange--text"
                            style="font-size: 18px; height: 18px; width: 18px"
                            ><svg
                              xmlns="http://www.w3.org/2000/svg"
                              viewBox="0 0 24 24"
                              role="img"
                              aria-hidden="true"
                              class="v-icon__svg"
                              style="font-size: 18px; height: 18px; width: 18px"
                              color="orange"
                            >
                              <path
                                d="M12,17.27L18.18,21L16.54,13.97L22,9.24L14.81,8.62L12,2L9.19,8.62L2,9.24L7.45,13.97L5.82,21L12,17.27Z"
                              ></path></svg>
                          </span>
                        </span>


                        <span v-if="halfRating">
                          <span
                            aria-hidden="true"
                            class="v-icon notranslate mb-1 theme--light orange--text"
                            style="font-size: 18px; height: 18px; width: 18px"
                            ><svg
                              xmlns="http://www.w3.org/2000/svg"
                              viewBox="0 0 24 24"
                              role="img"
                              aria-hidden="true"
                              class="v-icon__svg"
                              style="font-size: 18px; height: 18px; width: 18px"
                              color="orange"
                            >
                            <path d="M12 2L9.19 8.62L2 9.24L7.45 13.97L5.82 21L12 17.27V2Z"></path></svg>
                          </span>
                        </span>


 
                      </div>
                    </span>
                  </div>
                </div>
                <div id="catalog-body">
                  <p class="mb-0 py-1 text-body-2" style="color:rgba(0, 0, 0, .6);">{{ hotel.catalog?.category }}</p>
                  <p class="mb-0 pb-1 text-body-2">
                    <span>{{ hotel.address_line }}{{hotel.name_suffix?', '+hotel.name_suffix:''}}</span> <span> {{ hotel.catalog?.postal_code }} </span>
                  </p>
                </div>
                <div
                  v-if="hotel.catalog?.review_count>0"
                  id="review-desktop"
                  aria-hidden="true"
                  class="d-flex align-center py-1 text-body-2"
                >

                  <v-progress-circular
                    :color="reviewColor"
                    :model-value="reviewRate"
                  >{{ reviewRate }}</v-progress-circular>

                  <span class="pl-2 text-capitalize">{{reviewResult}} ·&nbsp;</span> <span> {{hotel.catalog?.review_count}} reviews</span>
                </div>
                <p id="headline-desktop" class="mb-0 pt-1">
                  <span class="text-body-2"></span>
                </p>
              </v-col>
              <v-col cols="12"  v-if="loading">

                <v-skeleton-loader
                  :loading="loading"
                  height="140"
                   type="avatar, list-item-three-line"
                ></v-skeleton-loader>

              </v-col>
            </v-row>
          </div>
  
  
          
        </v-col>
  
        <v-col cols="12">
  
  
  
  
          <hr data-v-d718b0cc="" role="separator" aria-orientation="horizontal" class="v-divider theme--light mt-1" style="margin-bottom: -1px; border-color: rgba(0, 0, 0, .12);">
  
          <v-sheet  v-if="!loading">
            <v-tabs
              v-model="currentTab"
              :items="tabs"
              align-tabs="center"
              height="60"
              slider-color="primary"
            >
              <template v-slot:tab="{ item }">
                <v-tab
                  :prepend-icon="item.icon"
                  :text="item.text"
                  :value="item.value"
                  size="small"
                  variant="plain"
                  color="primary"
                ></v-tab>
              </template>
  
              <template v-slot:item="{ item }">
  
                <v-tabs-window-item :value="item.value" class="py-4">
                 
  
                  <component :is="currentTab">
                  </component>
                  <div v-if="currentTab === 'tab-deals'">

                    <div v-if="noRoomAvaiable">
                      <div class="text-center py-4">
                        <i class="mdi mdi-text-box-search-outline text-disabled" style="font-size:35px;"></i>
                        <p class="priceDiv text-grey-darken-1">No available room on {{formattedDateRange()}} for {{guestPerRoom}} guests per room and {{numberOfRooms}} room.</p>
                        <v-btn class="rounded-xl text-none my-3" color="#1a73e8" variant="flat">Modify Search</v-btn>
                      </div>

                      
                    </div>
                    <div v-if="!noRoomAvaiable">
                     

                      <v-row v-for="(value, key) in sortedRoom" :key="key">
                      
                        <v-col cols="12" sm="4">
                          <div v-if="value.room_images.length==0">
                            

                            <v-img
                                :lazy-src="'/img/fallback-room.png'"
                                :src="'/img/fallback-room.png'"
                                aspect-ratio="16/9"
                                :height="150"
                                :class="rounded-lg"
                                cover
                            ></v-img> 

                          </div>
                          <div v-if="value.room_images.length!==0">
                            <div v-for="(v, k) in value.room_images" :key="k">
                              <div v-if="k == 0">
                                <v-img
                                :lazy-src="`${v.size_sm}`"
                                :src="`${v.size_sm}`"
                                v-on:error="v.size_sm='/img/fallback-global.png'" 
                                aspect-ratio="16/9"
                                :height="150"
                                :class="{'rounded-lg': value.room_images.length < 4, 'rounded-t-lg mb-1': value.room_images.length+1 >= 4}"
                                cover
                              ></v-img>                           
                              </div >
                              <div v-if="k+1 == 4">
                                <v-row no-gutter>
                                  <v-col cols="4" class="pe-0">
                                    <v-img
                                      :lazy-src="`${value.room_images[k-2].size_sm}`"
                                      :src="`${value.room_images[k-2].size_sm}`"
                                      v-on:error="value.room_images[k-2].size_sm='/img/fallback-global.png'" 
                                      aspect-ratio="16/9"
                                      :height="70"
                                      class="bg-grey-lighten-2 rounded-bs-lg"
                                      cover
                                    ></v-img>  
                                  </v-col>
                                  <v-col cols="4" class="px-1">
                                    <v-img
                                      :lazy-src="`${value.room_images[k-1].size_sm}`"
                                      :src="`${value.room_images[k-1].size_sm}`"
                                      v-on:error="value.room_images[k-1].size_sm='/img/fallback-global.png'" 
                                      aspect-ratio="16/9"
                                      :height="70"
                                      class="bg-grey-lighten-2 rounded-0"
                                      cover
                                    ></v-img>  
                                  </v-col>
                                  <v-col cols="4" class="ps-0">
                                    <v-img
                                      :lazy-src="`${value.room_images[k].size_sm}`"
                                      :src="`${value.room_images[k].size_sm}`"
                                      v-on:error="value.room_images[k].size_sm='/img/fallback-global.png'" 
                                      aspect-ratio="16/9"
                                      :height="70"
                                      class="bg-grey-lighten-2 rounded-be-lg"
                                      cover
                                    ></v-img>  
                                  </v-col>
                                </v-row>
                              </div>
                            </div>
                          </div>

                        </v-col>
                        <v-col cols="12" sm="8">
                          
                          <v-card color="rgba(0,0,0,.12)" variant="outlined" class="rounded-t-lg">
                            <v-sheet background="white">
                              <v-row class="pa-3">
                                <v-col cols="8">
                                  <h4 class="font-weight-medium mb-2">{{ value.room_name }}</h4>
                                  <div class="d-flex">

                                    <span aria-hidden="true" class="v-icon notranslate theme--light mt-1 me-1" style="font-size: 18px; height: 18px; width: 18px;"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" role="img" aria-hidden="true" class="v-icon__svg" style="font-size: 18px; height: 18px; width: 18px;" ><path d="M20 10V7A2 2 0 0 0 18 5H6A2 2 0 0 0 4 7V10A2 2 0 0 0 2 12V17H3.33L4 19H5L5.67 17H18.33L19 19H20L20.67 17H22V12A2 2 0 0 0 20 10M13 7H18V10H13M6 7H11V10H6M20 15H4V12H20Z" color="grey"></path></svg></span>

                                    
                                    <p class="me-3 text-capitalize">{{value.room_bed_groups}}</p>

                                    

                                    <div v-if="value.room_size_sqm" class="d-flex">
                                      <span data-v-15d6fcda="" aria-hidden="true" class="v-icon notranslate theme--light  mt-1  mx-1" style="font-size: 16px; height: 16px; width: 16px;"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" role="img" aria-hidden="true" class="v-icon__svg" style="font-size: 16px; height: 16px; width: 16px;"><path d="M23,15H21V17H23V15M23,11H21V13H23V11M23,19H21V21C22,21 23,20 23,19M15,3H13V5H15V3M23,7H21V9H23V7M21,3V5H23C23,4 22,3 21,3M3,21H11V15H1V19A2,2 0 0,0 3,21M3,7H1V9H3V7M15,19H13V21H15V19M19,3H17V5H19V3M19,19H17V21H19V19M3,3C2,3 1,4 1,5H3V3M3,11H1V13H3V11M11,3H9V5H11V3M7,3H5V5H7V3Z" color="grey"></path></svg></span>
                                      <p>{{value.room_size_sqm}} m<sup>2</sup></p>
                                    </div>



                                  </div>
  

                                </v-col>
                                <v-col cols="4" class="text-end">
                                  <v-btn size="small"  variant="flat" class="text-blue-darken-3 bg-white rounded-xl text-none">See Details</v-btn>
                                </v-col>
                              </v-row>
                              
                            </v-sheet>

                            <v-sheet v-for="roomGroup in value.rooms" :key="roomGroup.id">
                              <hr style="border-color:rgba(0,0,0,.12) !important;">
                              <div>

                                
                                <v-row class="pa-3">
                                  <v-col cols="8" style="flex: 1 0 0%; min-width: 0px">
                                    <div>
                                      <div id="meal-plan" class="d-flex align-baseline">

                                        <div v-if="roomGroup.meal_plan_description==''">
                                          <span class="icon-slash-effect">
                                            <i class="mdi mdi-silverware-fork-knife"></i>
                                          </span>
                                          <span class="ms-2">Without breakfast</span>
                                        </div>

                                        <div v-if="roomGroup.meal_plan_description!==''">
                                          <i class="mdi mdi-silverware-fork-knife text-success"></i>
                                          <span class="ms-2 text-success">{{roomGroup.meal_plan_description}}</span>
                                        </div>

                                      </div>
                                    </div>
                                    <div v-if="roomGroup.cancel_policy_description!==''" id="cancel-policy" class="d-flex align-baseline text-red">
                                      <span>
                                        <span aria-hidden="true" class="v-icon notranslate v-icon--left theme--light error--text" style="font-size: 17px; height: 17px; width: 17px">
                                          <svg
                                            xmlns="http://www.w3.org/2000/svg"
                                            viewBox="0 0 24 24"
                                            role="img"
                                            aria-hidden="true"
                                            class="v-icon__svg"
                                            style="font-size: 17px; height: 17px; width: 17px"
                                          >
                                            <path
                                              d="M0.93,4.2L2.21,2.93L20,20.72L18.73,22L16.73,20H4C2.89,20 2,19.1 2,18V6C2,5.78 2.04,5.57 2.11,5.38L0.93,4.2M20,8V6H7.82L5.82,4H20A2,2 0 0,1 22,6V18C22,18.6 21.74,19.13 21.32,19.5L19.82,18H20V12H13.82L9.82,8H20M4,8H4.73L4,7.27V8M4,12V18H14.73L8.73,12H4Z"
                                            ></path>
                                          </svg>
                                        </span>
                                      </span>
                                      <span class="ms-2 align-middle text-body-2 text-md-body-1 error--text">Non-refundable</span>
                                    </div>
                                  </v-col>
                                  <v-col cols="4" class="d-flex justify-end " style="flex: 0 0 auto; min-width: 144px">
                                    <div class="d-inline-block my-n1">
                                      <div class="d-flex flex-row flex-nowrap align-center" style="gap: 4px">
                                        <button
                                          type="button"
                                          class="v-btn v-btn--icon v-btn--round theme--light v-size--x-small"
                                          style="height: 32px; width: 32px"
                                        >
                                          <span class="v-btn__content">
                                            <span
                                              aria-hidden="true"
                                              class="v-icon notranslate theme--light"
                                              style="font-size: 20px; height: 20px; width: 20px"
                                            >
                                              <svg
                                                xmlns="http://www.w3.org/2000/svg"
                                                viewBox="0 0 24 24"
                                                role="img"
                                                aria-hidden="true"
                                                class="v-icon__svg"
                                                style="font-size: 20px; height: 20px; width: 20px"
                                              >
                                                <path
                                                  d="M19,21H8V7H19M19,5H8A2,2 0 0,0 6,7V21A2,2 0 0,0 8,23H19A2,2 0 0,0 21,21V7A2,2 0 0,0 19,5M16,1H4A2,2 0 0,0 2,3V17H4V3H16V1Z"
                                                ></path>
                                              </svg>
                                            </span>
                                          </span>
                                        </button>

                                        <span data-v-4ff6fa5e="" class="v-tooltip v-tooltip--top"></span>
                                        <span data-v-4ff6fa5e="" class="v-tooltip v-tooltip--top"></span>

                                        <button 
                                          type="button"
                                          class="v-btn v-btn--icon v-btn--round theme--light v-size--x-small"
                                          style="height: 32px; width: 32px"
                                        >
                                          <span class="v-btn__content">
                                            <span
                                              aria-hidden="true"
                                              class="v-icon notranslate theme--light"
                                              style="font-size: 22px; height: 22px; width: 22px"
                                              >
                                              <svg
                                                xmlns="http://www.w3.org/2000/svg"
                                                viewBox="0 0 24 24"
                                                role="img"
                                                aria-hidden="true"
                                                class="v-icon__svg"
                                                style="font-size: 22px; height: 22px; width: 22px"
                                              >
                                                <path
                                                  d="M19 19H15V21H19C20.1 21 21 20.1 21 19V15H19M19 3H15V5H19V9H21V5C21 3.9 20.1 3 19 3M5 5H9V3H5C3.9 3 3 3.9 3 5V9H5M5 15H3V19C3 20.1 3.9 21 5 21H9V19H5V15M7 11H9V13H7V11M11 11H13V13H11V11M15 11H17V13H15V11Z"
                                                ></path>
                                              </svg>
                                            </span>
                                          </span>
                                        </button>

                                        <div class="v-dialog__container"></div>
                                        <span class="v-tooltip v-tooltip--top"></span>
                                          <button
                                            type="button"
                                            class="v-btn v-btn--icon v-btn--round theme--light v-size--x-small"
                                            id="stay-availability-menu-button"
                                            role="button"
                                            aria-haspopup="true"
                                            aria-expanded="false"
                                            style="height: 32px; width: 32px"
                                          >
                                          <span class="v-btn__content">
                                            <span
                                              data-v-4ff6fa5e=""
                                              aria-hidden="true"
                                              class="v-icon notranslate theme--light"
                                              style="font-size: 22px; height: 22px; width: 22px"
                                            >
                                              <svg
                                                xmlns="http://www.w3.org/2000/svg"
                                                viewBox="0 0 24 24"
                                                role="img"
                                                aria-hidden="true"
                                                class="v-icon__svg"
                                                style="font-size: 22px; height: 22px; width: 22px"
                                              >
                                                <path
                                                  d="M16,12A2,2 0 0,1 18,10A2,2 0 0,1 20,12A2,2 0 0,1 18,14A2,2 0 0,1 16,12M10,12A2,2 0 0,1 12,10A2,2 0 0,1 14,12A2,2 0 0,1 12,14A2,2 0 0,1 10,12M4,12A2,2 0 0,1 6,10A2,2 0 0,1 8,12A2,2 0 0,1 6,14A2,2 0 0,1 4,12Z"
                                                >
                                                </path>
                                              </svg>
                                            </span>
                                          </span>
                                          </button>
                                      </div>
                                      <div >
                                        <div class="v-dialog__container"><!----></div>
                                        <div>
                                          <!---->
                                          <div class="v-dialog__container"><!----></div>
                                        </div>
                                      </div>
                                    </div>
                                  </v-col>

                                  <v-col cols="8" class="px-4 pb-4 pt-0">
                                    <div class="mt-1 mb-2">
                                      <div class="d-inline-flex align-stretch flex-wrap justify-split  me-2" style="gap: 4px">
                                        <div class="promoDiv"  v-if="roomGroup.pricing_data?.saving_pct>0">
                                          <span class="text-button white--text font-weight-medium text-uppercase text-no-wrap bg-red py-1 px-2" style="font-family: inherit !important">
                                            Save
                                            <span class="text-caption text-md-h8 font-weight-bold" style="font-family: inherit !important">
                                              {{ Math.round((roomGroup.pricing_data?.saving_pct + roomGroup.pricing_data?.cashback_pct + roomGroup.pricing_data?.bonus_cashback_pct)* 100) }} %
                                            </span>
                                            Today!
                                          </span>
                                        </div>
                                        <div class="roomLeftDiv" v-if="roomGroup.room_available<5">
                                          <span v-if="roomGroup.room_available>3" class="text-button font-weight-medium text-uppercase text-no-wrap bg-orange-lighten-5 py-1 px-2 text-orange-darken-2" style="font-family: inherit !important">{{roomGroup.room_available}} rooms left</span>
                                          <span v-if="roomGroup.room_available<=3" class="text-button font-weight-medium text-uppercase text-no-wrap bg-red-lighten-5 py-1 px-2 text-red" style="font-family: inherit !important">{{roomGroup.room_available}} rooms left</span>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="mt-n1" v-if="roomGroup.pricing_data?.strikethrough_rate_nightly !== null">
                                      <p class="promoText font-weight-medium">Rp {{ formatCurrency(roomGroup.pricing_data?.strikethrough_rate_nightly) }}</p>
                                    </div>
                                    <div>
                                      <p class="d-block mb-0 priceDiv">
                                        <span class="text-h8 font-weight-medium"> Rp </span>
                                        <span class="font-weight-medium  priceTotal" style="line-height: 1 !important">
                                          {{ formatCurrency(roomGroup.pricing_data?.net_rate_nightly_with_bonus) }}
                                        </span>
                                        <span class="text-h8"> / night</span>
                                        <span aria-hidden="true" class="v-icon notranslate mt-n2 text-disabled theme--light" style="font-size: 8px; height: 8px; width: 8px">
                                          <svg
                                            xmlns="http://www.w3.org/2000/svg"
                                            viewBox="0 0 24 24"
                                            role="img"
                                            aria-hidden="true"
                                            class="v-icon__svg"
                                            style="font-size: 8px; height: 8px; width: 8px"
                                          >
                                            <path
                                              d="M10,2H14L13.21,9.91L19.66,5.27L21.66,8.73L14.42,12L21.66,15.27L19.66,18.73L13.21,14.09L14,22H10L10.79,14.09L4.34,18.73L2.34,15.27L9.58,12L2.34,8.73L4.34,5.27L10.79,9.91L10,2Z"
                                            >
                                            </path>
                                          </svg>
                                        </span>
                                      </p>
                                    </div>


                                    <div v-if="roomGroup.pricing_data?.net_price_total_with_bonus!==roomGroup.pricing_data?.net_price_total_with_bonus">
                                      <p class="priceAll">
                                        Total&nbsp;·&nbsp;Rp {{ formatCurrency(roomGroup.pricing_data?.net_price_total_with_bonus) }}
                                      </p>
                                    </div>
                                    <div class="" style="grid-area: tax-fees">
                                      <p class="mb-0 text-button text-sm-caption text-disabled">
                                        after tax &amp; fees
                                      </p>
                                    </div>
                                    
                                    <div class="divNotes pt-2">
                                      <p class="mb-0 d-flex justify-start align-center">
                                        <span aria-hidden="true" class="v-icon notranslate mr-1 text-disabled theme--light" style="font-size: 8px; height: 8px; width: 8px">
                                          <svg
                                            xmlns="http://www.w3.org/2000/svg"
                                            viewBox="0 0 24 24"
                                            role="img"
                                            aria-hidden="true"
                                            class="v-icon__svg"
                                            style="font-size: 8px; height: 8px; width: 8px"
                                          >
                                            <path
                                              d="M10,2H14L13.21,9.91L19.66,5.27L21.66,8.73L14.42,12L21.66,15.27L19.66,18.73L13.21,14.09L14,22H10L10.79,14.09L4.34,18.73L2.34,15.27L9.58,12L2.34,8.73L4.34,5.27L10.79,9.91L10,2Z"
                                            >
                                            </path>
                                          </svg>
                                        </span>
                                        <span class="text-none text-disabled">Member-only price, valid in app only</span>
                                      </p>                                
                                    </div>


                                  </v-col>
                                  <v-col cols="4" class="text-end position-relative px-4 pb-4 pt-0  d-flex flex-column align-end justify-center">
                                    <button  type="button" class="v-btn-choose my-1 v-btn v-btn--has-bg theme--light v-size--default color-primary btnBook btnPrimary py-3" style="opacity: 1;">
                                      <span class="text-none text-white">Book Now</span>
                                    </button>


                                    <p class="position-absolute bottom-0 right-0 pa-4" style="font-size: .75rem !important;">
                                      <span aria-hidden="true" class="v-icon notranslate theme--light primary--text txtPrimary" style="font-size: 12px; height: 12px; width: 12px;">
                                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" role="img" aria-hidden="true" class="v-icon__svg" style="font-size: 12px; height: 12px; width: 12px;"><path d="M12,17.27L18.18,21L16.54,13.97L22,9.24L14.81,8.62L12,2L9.19,8.62L2,9.24L7.45,13.97L5.82,21L12,17.27Z"></path></svg>
                                      </span> 
                                      <span class="text-none txtPrimary">
                                      Collect {{roomGroup.pricing_data?.wisata_point}} points
                                      </span>
                                    </p>

                                  </v-col>
                                </v-row>

                                
                              </div>
                            </v-sheet>
                            
                          </v-card>

                        </v-col>
                      </v-row>

                    </div>

  
                  </div>
                  <div v-if="currentTab === 'tab-photos'">
                  
                    <div class="text-center" v-if="loading">Loading...</div>
                    <div class="text-center" v-else-if="!images.length">No images.</div>
                    <div v-else-if="images.length">
                      <v-row no-gutters>
                        <v-col 
                        cols="4" 
                        class="pa-md-4"
                        v-for="(image, index) in images" :key="index">
  
                          <v-img
                            :lazy-src="`${image.url.md}`"
                            :src="`${image.url.md}`"
                            v-on:error="image.url.md='/img/fallback-global.png'" 
                            aspect-ratio="1"
                            class="bg-grey-lighten-2"
                            cover
                          ></v-img>
  
                        </v-col>
                      </v-row>
                      
  
  
  
  
                    </div>
  
  
  
                  </div>
                  <div v-if="currentTab === 'tab-info'">
  
                    <h2 class="text-h5 text-sm-h4 font-weight-medium mb-2">
                      About the property
                    </h2>
                    <p>
                      {{ hotel.general_info?.descriptions?.location }}
                    </p>
                    <p>
                      {{ hotel.general_info?.descriptions?.dining }}
                    </p>
                    <p>
                      {{ hotel.general_info?.descriptions?.amenities }}
                    </p>

                    <p class="font-weight-medium mb-2 mt-4">
                      Languages
                    </p>
                    <p>
                      <span>
                        <span v-for="(langs, idx, x) in hotel.general_info?.spoken_languages" :key="langs.index">
                         <span v-if="x + 1 !== hotel.general_info?.spoken_languages.length && x !==0">,</span> {{ langs.name }}
                        </span>
                      </span>
                    </p>

                    <section class="pt-8 pt-sm-12">


                      <h3 class="text-h5 text-sm-h4 font-weight-medium mb-2">
                        Policies
                      </h3>
                      <div class="d-flex">
                        <div>
                          <p class="font-weight-medium">
                            Check-in
                          </p>
                          <p>{{hotel.important_info?.checkin?.begin_time}}</p>
                        </div>
                        <div class="pl-6">
                          <p class="font-weight-medium">
                            Check-out
                          </p>
                          <p>{{hotel.important_info?.checkout?.time}}</p>
                        </div>
                      </div>

                      <div class="pt-4 pt-sm-6">
                        <p class="font-weight-medium">
                          Additional check-in information
                        </p>
                        <div v-html="hotel.important_info?.checkin?.instructions"></div>
                      </div>

                      <div class="pt-4 pt-sm-6">
                        <p class="font-weight-medium">
                          Others
                        </p>
                        <div v-html="hotel.important_info?.policies?.know_before_you_go"></div>
                      </div>
                      
                    
                    </section>
                    <section class="pt-8 pt-sm-12">
                      <h2 class="text-sm-h4 text-sm-h4 font-weight-medium mb-2">
                        Important information
                      </h2>
                      <h4 class="font-weight-medium">
                        Mandatory Charges
                      </h4>
                        <div class="mb-3" v-html="hotel.important_info?.fees?.mandatory"></div>
                      
                        
                      <h4 class="font-weight-medium">
                        Optional charges
                      </h4>
                        <div class="mb-3" v-html="hotel.important_info?.fees?.optional"></div>

                    </section>

                    
                  </div>
  
                  
                </v-tabs-window-item>
  
  
              </template>
            </v-tabs>
          </v-sheet>

          

        </v-col>
      </v-row>
    </v-container>
  
    <v-container max-width="1000" v-if="notFound">
      <h3>404 not found</h3>
      <a class="text-none" :href="'/'">Home page</a>
    </v-container>
  
  </template>
  
  
  <script setup>
  import { ref, watch } from 'vue';
  import { useRoute } from 'vue-router'
  console.log('setup..')
  const route = useRoute()
  
  // Extract hotel name and hotel ID from the path
  const hotelNameWithId = route.params.hotelName // 'fairmont-jakarta-9000248394'
  const hotelId = hotelNameWithId.split('-').pop() // '9000248394'
  
  // Extract query parameters
  const checkin = route.query.checkin // '2025-03-04'
  const checkout = route.query.checkout // '2025-03-08'
  const guestPerRoom = route.query.guest_per_room // '2'
  const numberOfRooms = route.query.number_of_room // '1'
  

  console.log('hotelId : ',hotelId)
  console.log('checkin : ',checkin)
  console.log('checkout : ',checkout)
  console.log('guestPerRoom : ',guestPerRoom)
  console.log('numberOfRooms : ',numberOfRooms)
  


  
  // Reactive state for tabs
  const currentTab = ref('tab-deals');
  const tabs = ref([
    {
      icon: 'mdi-tag-outline',
      text: 'Deals',
      value: 'tab-deals',
    },
    {
      icon: 'mdi-grid',
      text: 'Photos',
      value: 'tab-photos',
    },
    {
      icon: 'mdi-information-outline',
      text: 'Info',
      value: 'tab-info',
    },
  ]);
  
  const data = ref(null);
  
  const hotel = ref([]);
  const images = ref([]);
  const loading = ref(true);
  const notFound = ref(false);
  const noRoomAvaiable = ref(false);

  const rating  = ref(0);
  const halfRating  = ref(false);

  const reviewColor =ref();
  const reviewResult =ref('');
  const reviewRate = ref(0);

  const hotelAvatar = ref('/img/fallback-property.png');

  const language = ref([])

  const rooms = ref([])
  
  const availableRooms = ref([])
  const sortedRoom = ref([])


  function formattedDateRange() {
      const start = new Date(checkin);
      const end = new Date(checkout);
      
      const dayStart = start.getDate();
      const dayEnd = end.getDate();
      const month = start.toLocaleString('default', { month: 'short' });
      const year = start.getFullYear();

      return `${dayStart} – ${dayEnd} ${month} ${year}`;
  }

  // Function to fetch data based on the selected tab
  async function fetchData() {
    loading.value = true;
    try {
      const response = await fetch('https://tempest.project-exterior.com/property/content?id='+hotelId+'&include=room&include=image&include=important_info&include=general_info');
      if (!response.ok) {
        alert('Network response was not ok')
        throw new Error('Network response was not ok');
      }
      data.value = await response.json();
      // console.log('data.value : ',data.value['9000248394'].image)
      images.value = data.value[hotelId].image

      hotel.value = data.value[hotelId]
  
      var rate = data.value[hotelId].catalog.star_rating
      console.log('rate : ',rate)
      rating.value =  Math.floor(rate)

      halfRating.value = (rate % 1 !== 0) ? true:false;


      var reviewScore = data.value[hotelId].catalog.review_rating
      reviewRate.value = reviewScore
      console.log('reviewScore : ',reviewScore)
      if(reviewScore<75){
        reviewColor.value ='orange';
      } 
      else if(reviewScore<90){
        reviewColor.value ='green-darken-3';
      } 
      else {
        reviewColor.value ='purple';
      }

      if(reviewScore<75){
        reviewResult.value ='average';
      } 
      else if(reviewScore<80){
        reviewResult.value ='good';
      } 
      else if(reviewScore<90){
        reviewResult.value ='Very Good'; 
      }
      else {
        reviewResult.value ='excellent'; 
      }
      console.log('reviewResult : ',reviewResult)
      console.log('reviewRate : ',reviewRate)

      if(data.value[hotelId].catalog.hero_image_url){
        if(data.value[hotelId].catalog.hero_image_url.sm){
          hotelAvatar.value = data.value[hotelId].catalog.hero_image_url.sm
        }
        
      }
      console.log('hotel.general_info?.descriptions?.spoken_languages' ,data.value[hotelId].general_info.spoken_languages)


      language.value =  data.value[hotelId].general_info.spoken_languages

      rooms.value =  data.value[hotelId].room

    } catch (error) {
      console.error('Fetch error:', error);
    } finally {
      loading.value = false;
    }
  }

  async function checkAvailability() {
    loading.value = true;
    try {
      const response = await fetch('https://tempest.project-exterior.com/stay/availability/'+hotelId+'?checkin='+checkin+'&checkout='+checkout+'&guest_per_room='+guestPerRoom+'&number_of_room='+numberOfRooms+'&run_price_check=false');


      if (!response.ok) {
        //alert('Network response was not ok')
        notFound.value=true
        throw new Error('Network response was not ok');
      }
      data.value = await response.json();
      // console.log('data.value : ',data.value['9000248394'].image)
      availableRooms.value = data.value.offers
      if(data.value.offers.length==0){
        noRoomAvaiable.value=true
      }
      combinedRooms()
    } catch (error) {
      console.error('Fetch error:', error);
    } finally {
      loading.value = false;
    }
  }
  
  
  console.log('rating : ',rating)
      console.log('halfRating : ',halfRating)
      console.log('language : ',language)     

  // Watch for changes to the selected tab and fetch data
  // watch(currentTab, () => {
  //   fetchData();
  // });
  
  // Function to handle tab clicks
  function tabClicked(value) {
    currentTab.value = value;
  }

  function formatCurrency(value) {
    if(value){
      return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    } else {
      return ''
    }
     
  }


function combinedRooms() {
  const map = new Map();

  availableRooms.value.forEach(room => {
    const { room_images, room_name, room_bed_groups, room_size_sqm } = room;
    const { id } = room.room_data

    // Check if the room's room_data.id is already in the map
    if (!map.has(id)) {
      map.set(id, {
        id,
        room_images,
        room_name,
        room_bed_groups,
        room_size_sqm,
        rooms: []
      });
    }
    // Add the room to the appropriate group
    map.get(id).rooms.push(room);
  });

  // Convert the map values to an array
  const result = Array.from(map.values());
  
  console.log('result: ', result);
  sortedRoom.value = result
  //return result;
}
  
  // Initial data fetch
  fetchData();
  checkAvailability();
  
  </script>
  
  
  
  <style scoped>
  
  #stay :deep(.v-tab__slider) {
    top: 0 !important;
  }
  
  #stay .v-btn--variant-plain.v-tab-item--selected{
    opacity: 1;
  }

  .icon-slash-effect {
    position: relative;
    display: inline-block;
  }
  .icon-slash-effect i {
    color:#757575;
  }
  .icon-slash-effect::before {
    content: '';
    position: absolute;
    width: 100%;
    height: 2px;
    border-top: 1px solid #fff;
    background-color: #757575;
    transform: rotate(50deg);
    top: 50%;
    left: 0;
    transform-origin: center;
    z-index: 1; /* Place it behind the icon */
  }
  </style>