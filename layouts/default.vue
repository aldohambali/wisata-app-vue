<template>
  <v-app>

    <v-app-bar class="elevation-0" style="height: 64px;box-shadow: 0 1px 0 0 rgba(0, 0, 0, .12) !important;" app fixed>
      <v-container max-width="1000" class="py-1 py-md-2">
        <v-row align="center" justify="space-between">
          <!-- Left Side: Logo -->
          <v-col cols="3" class="">
            <router-link to="/" class="logo-link">
            <v-img
              src="/img/logo.png"
              alt="Logo"
              height="42"
              contain
            ></v-img>
          </router-link>
          </v-col>

          <!-- Center: Search Bar -->
          <v-col cols="7" class="d-flex align-center">
            <!-- <v-text-field
              v-model="search"
              label="Search"
              append-icon="mdi-magnify"
              solo
              hide-details
              class="flex-grow-1"
            ></v-text-field> -->
            <v-btn class="text-none" prepend-icon="mdi-magnify" color="#f5f5f5" variant="flat" @click="onSearch = !onSearch" block>
              <div v-if="hotelNameWithId!==''">
                <span class="text-capitalize">{{hotelName()}}</span>  · {{ formatDateRange(checkin,checkout) }}
              </div>
              <div v-if="hotelNameWithId==''">Search</div>
                
            </v-btn>


          </v-col>

          <!-- Right Side: Login Button -->
          <v-col cols="2" class="d-flex justify-end">
            <v-btn 
            class="text-none"
            color="#1a73e8"
            variant="flat"            
            @click="goAbout">Sign In</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-app-bar>



    <v-main>


      <v-overlay
          v-model="onSearch"
          class="bottom center"
          contained
          style="top:64px; width:100%;"
          hide-overlay
        >

          <v-dialog v-model="onSearch" fullscreen hide-overlay style="top:64px;">
            <div>

             <v-container class="mt-0 bg-white rounded-b-lg" max-width="1000">
              <v-row no-glutter>
                <v-col cols="12" sm="4" class="pa-1">
                  <v-text-field 
                  color="primary" 
                  clearable
                  variant="outlined" 
                  placeholder="Where are you going?" 
                  label="Where are you going?" 
                  prepend-inner-icon="mdi-map-marker-outline"
                  v-model="searchQuery"
                  @input="onInput"
                  :loading="searchLoading"
                  ></v-text-field>



                  

                  <v-list class="position-absolute overflow-y-auto pa-0" style="max-height: 350px; min-width: 400px;">
                    <v-list-item-group v-if="searchResponse?.length">
                      <v-list-item v-for="item in searchResponse" :key="item.id" class="searchList" @click="selectItem(item)">
                        <v-list-item-content>
                          <v-btn 
                          v-if="item.location_type=='property'" 
                          size="x-small"
                          prepend-icon="mdi-bed-outline" 
                          variant="tonal"
                          color="info" 
                          >
                            Hotel
                          </v-btn>
                          <div class="ma-1">
                            <div class="priceDiv" v-html="highlightSearchTerm(item.name,searchQuery)"></div>
                            <small class="text-grey-darken-1">{{ item.name_suffix }}</small>
                          </div>

                        </v-list-item-content>
                      </v-list-item>


                      
                    </v-list-item-group>

                  </v-list>


                </v-col>
                <v-col cols="12" sm="3" class="pa-1">
                  <!-- <v-text-field clearable label="Check in - Check out" variant="outlined"> </v-text-field> -->
                  <v-row justify="center">
                    <!-- <v-date-picker v-model="dateRange"></v-date-picker> -->

                    <v-col>
                      <v-date-input
                        v-model="dateRange"
                        multiple="range"
                        prepend-icon=""
                        prepend-inner-icon="mdi-calendar-today-outline"
                        label="Check in - Check out" variant="outlined"
                        :min="minDate"
                      ></v-date-input>
                    </v-col>

                  </v-row>


                 
                </v-col>
                <v-col cols="12" sm="3" class="pa-1">
                  <v-row>
                    <v-col cols="6">
                      <v-text-field clearable label="Guests" variant="outlined" type="number" v-model="totalGuest"></v-text-field>
                    </v-col>
                    <v-col cols="6">
                      <v-text-field clearable label="Rooms" variant="outlined" type="number" v-model="totalRoom"></v-text-field>
                    </v-col>
                  </v-row>
                  
                </v-col>
                <v-col cols="12" sm="2" class="pa-1">
                  <v-btn 
                  size="x-large"
                  prepend-icon="mdi-magnify"
                  class="text-none"
                  color="#1a73e8"
                  variant="flat"         
                  v-if="!redirectPage"     
                  @click="goStay">
                  Search
                </v-btn>
                <v-btn  v-if="redirectPage"                      
                size="x-large"
                  prepend-icon="mdi-magnify"
                  class="text-none"
                  color="#1a73e8"
                  variant="flat" disabled="">Loading..</v-btn>
                </v-col>
              </v-row>
             </v-container>



            </div>

            
          </v-dialog>

        </v-overlay>

      <NuxtPage />
    </v-main>

    <v-footer variant="flat" color="#f5f5f5" app>

      <v-container  style="max-width: 1000px; position: relative;">
        <v-row align="center" justify="space-between">
          <v-col cols="6" class="py-1">

            <div class="d-flex flex-row">
                <div variant="text" class="text-button text-no-wrap text-none"><p>©&nbsp;Wisata App</p></div>
                <div variant="text" class="text-button text-no-wrap text-none pl-2"><span class="mx-3">·</span>
                <v-btn
                  href="/termsandcondition"
                  target="_blank"
                  label="Terms"
                  class="link text-h8 text-none"
                  variant="plain"
                >
                  Terms
                </v-btn>            
                </div>
            </div>

          </v-col>
          <v-col cols="6" class="text-right py-1">
            <div variant="text" class="text-button text-no-wrap text-none">v4.10</div>
          </v-col>
        </v-row>
      </v-container>



    </v-footer>
  </v-app>
</template>



  
<script setup>
  import { ref, watch } from 'vue';
  import { useRoute } from 'vue-router'
  console.log('setup..')
  const route = useRoute()
  

  const onSearch = ref(false);
  console.log('onSearch : ',onSearch)
  const dateRange = ref([])


  console.log('dateRange : ',dateRange)

  const totalGuest = ref(2)
  const totalRoom = ref(1)
  const minDate= ref(getTomorrowDate())

  function getTomorrowDate() {
      const tomorrow = new Date();
      tomorrow.setDate(tomorrow.getDate() + 1); // Set to tomorrow
      return tomorrow.toISOString().split("T")[0]; // Format to YYYY-MM-DD
  }

  function formatDateRange(startDateStr, endDateStr) {
    // Create date objects from the input strings
    const startDate = new Date(startDateStr);
    const endDate = new Date(endDateStr);

    // Extract the day, month, and year from the start date
    const startDay = startDate.getDate();
    const endDay = endDate.getDate();
    const month = startDate.toLocaleString('default', { month: 'short' }); // Get the short month name
    const year = startDate.getFullYear();

    // Construct the formatted date range
    return `${startDay} – ${endDay} ${month} ${year}`;
  }

  function highlightSearchTerm(text, searchTerm) {
    if (!searchTerm) return text; // If there's no search term, return the original text
    const regex = new RegExp(`(${searchTerm})`, 'gi'); // Create a regex to match the search term, case insensitive
    return text.replace(regex, '<strong>$1</strong>'); // Wrap matches in <strong> tags
  }

  // Extract hotel name and hotel ID from the path
  const hotelNameWithId = route.params.hotelName || '' // 'fairmont-jakarta-9000248394'
  const hotelId = hotelNameWithId.split('-').pop() // '9000248394'

  // Extract query parameters
  const checkin = route.query.checkin // '2025-03-04'
  const checkout = route.query.checkout // '2025-03-08'
  const guestPerRoom = route.query.guest_per_room // '2'
  const numberOfRooms = route.query.number_of_room // '1'

  console.log('checkin header : ',checkin)
  console.log('checkout header : ',checkout)
  console.log('guestPerRoom header : ',guestPerRoom)
  console.log('numberOfRooms header : ',numberOfRooms)
  console.log('hotelId header : ',hotelId)

  const searchQuery = ref('')
  const searchResponse = ref([])
  const searchLoading = ref(false)

  const selectedHotelId = ref('')
  const selectedHotelName = ref('')
  const selectedHotelNameLocation = ref('')
  const selectedSlug = ref('')

  const redirectPage = ref(false)

  function onDateChange(newDate) {
      // selectedDate = newDate; // Update selected date if needed
      console.log('newDate : ',newDate)
    }

  function hotelName() {
      const parts = hotelNameWithId.split('-');
      return parts.slice(0, -1).join(' ').replace(/-/g, ' ');
  }

  function selectItem(item) {
      // this.selectedItem = item; // Set the selected item
      console.log('item : ',item)
      selectedHotelId.value = item.id
      selectedHotelName.value = item.name 
      selectedHotelNameLocation.value = item.name+', '+item.name_suffix 
      selectedSlug.value = item.slug

      searchResponse.value = [];
      searchQuery.value =  item.name+', '+item.name_suffix 
  }


  async function onInput() {
      if (searchQuery.value.length < 3) {
        console.log('searchQuery.value ',searchQuery.value)
        searchResponse.value = [];
        return;
      }
      searchLoading.value = true;
      try {
      // const response = await fetch('https://tempest.project-exterior.com/location/search?query='+searchQuery.value);
      const response = await fetch('https://tempest.project-exterior.com/property/search?query='+searchQuery.value);

      if (!response.ok) {
        alert('Network response was not ok')
        throw new Error('Network response was not ok');
      }

      searchResponse.value = await response.json();
      console.log('response data.value : ',searchResponse.value)

      } catch (error) {
        console.error('API call failed:', error);
      } finally {
        searchLoading.value = false;
      }
  }



  function goHome() {
      this.$router.push('/');
  }
  function goStay() {

    // route.push('/stay/123');
    if(dateRange.value.length>0 && selectedSlug.value && totalGuest.value>0 && totalRoom.value>0) {
      console.log('dateRange : ',dateRange.value[0])

      var startDate = new Date(dateRange.value[0]); 
      var checkinVal = startDate.getFullYear()+'-'+String(startDate.getMonth() + 1).padStart(2, '0')+'-'+String(startDate.getDate()).padStart(2, '0')

      var endDate = new Date(dateRange.value[dateRange.value.length - 1]); 
      var checkoutVal = endDate.getFullYear()+'-'+String(endDate.getMonth() + 1).padStart(2, '0')+'-'+String(endDate.getDate()).padStart(2, '0')
      redirectPage.value=true

      window.location.href = '/stay/'+selectedSlug.value+'/?checkin='+checkinVal+'&checkout='+checkoutVal+'&guest_per_room='+totalGuest.value+'&number_of_room='+totalRoom.value;

    } else{
      alert('Please fill all the textbox')
    }




    // this.$router.push('/stay');
  }
  function goAbout() {
      this.$router.push('/about');
  }
</script>


<style scoped>
.v-footer {
  background-color: #1976d2;
  color: white;
  position: relative;
}
:deep(.v-overlay__scrim){
  top:64px;
}
</style>