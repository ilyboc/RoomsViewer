<template>
  <v-container>

    <v-flex xs12 md5>
      <v-select
        :items="items"
        label="Choose a hotel"
        solo
        v-model="current"
        prefix="Hotel "
      ></v-select>
    </v-flex>

    <v-flex xs12>
      <v-expansion-panel>
        <v-expansion-panel-content>

          <template v-slot:header>
            <div>Hotel Details</div>
          </template>

          <v-container fluid grid-list-md v-if="details">
            <v-layout wrap justify-center>
              <v-flex
                xs12
                sm6
                md4
                lg3
                v-for="props in unflatten(Object.keys(details),rows)"
                :key="props.toString()">
                <v-card>
                  <v-list dense>
                    <v-list-tile
                      :key="key"
                      v-for="key in props">
                      <v-list-tile-content>{{key}} </v-list-tile-content>
                      <v-list-tile-content class="align-end">{{details[key]}}</v-list-tile-content>
                    </v-list-tile>
                  </v-list>
                </v-card>
              </v-flex>
            </v-layout>
          </v-container>

        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-flex>
  
    <v-item-group class="viewer">
      <v-container grid-list-md>
        <v-layout column reverse>
          <v-layout  v-for="floor in floors" :key="floor" justify-around>

            <v-flex xs12 align-self-start>
              <v-item>
                <v-card
                :color="active ? 'primary' : ''"
                class="d-flex align-center"
                style="text-align: center;"
                white
                height="100"
                width="100">
                  <v-card-text>
                    floor {{floor}}
                  </v-card-text>
                </v-card>
              </v-item>
            </v-flex>

            <v-flex
              v-for="room in roomsByFloor(floor)"
              :key="room.room_num"
              xs12
              align-self-start>
              <v-item>
                <v-card
                  slot-scope="{ active, toggle }"
                  :color="active ? 'primary' : ''"
                  class="d-flex align-center"
                  dark
                  height="100"
                  width="100"
                  @click="toggle">
                  <v-scroll-y-transition>
                    <div style="text-align: left; padding-left: 5px;"
                      v-if="active">
                      <span>{{room.room_num + " " + room.room_type}}</span><br>
                      <span style="font-size: 8px;">{{name(room.room_type)}}</span>
                    </div>
                  </v-scroll-y-transition>
                  <v-card-text v-if="!active">
                    {{room.room_num + " " + room.room_type}}
                  </v-card-text>
                </v-card>
              </v-item>
            </v-flex>
          </v-layout>
        </v-layout>
      </v-container>
    </v-item-group>

  </v-container>
</template>

<script>
  import hotels from "./rooms-view.json"
  import roomTypes from "./roomtypes.json"
  import rooms from "./rooms.json"

  export default {
    data: () => ({
      rows: 4,
      items: Object.keys(hotels.facilities),
      current: "",
      active: false
    }),
    computed: {
      details: function() {
        let res = rooms.facilities[this.current]
        res? delete res["facility_id"] : undefined
        return res
      },
      floors: function() {
        let floor = 0
        if(this.current == "") return floor
        let rooms = hotels.facilities[this.current].rooms
        Object.keys(rooms).forEach(room => {
          if(rooms[room].room_floor > floor)
              floor = rooms[room].room_floor;
        })
        return floor;
      }

    },
    methods: {
      // example: [1, 2, 3, 4]  --2-->  [[1, 2], [3, 4]]
      unflatten: function(arr, n) {
        let res = []
        let e = []
        arr.forEach((v, i) => {
          if(i!=0 && i%n == 0) {
            res.push(e)
            e = [v]
          }
          else e.push(v)
        })
        if(e.length != 0)
            res.push(e)
        return res
      },
      roomsByFloor(floor){
        let res = []
        let rooms = hotels.facilities[this.current].rooms
        Object.keys(rooms).forEach(room => {
          if(rooms[room].room_floor == floor)
              res.push(rooms[room]);
        })
        return res
      },
      name(type) {
        return roomTypes[this.current][type].name
      }
    }
  }
</script>


<style>
.viewer {
  padding-top: 20px; 
  white-space: nowrap; 
  overflow: auto;
  border: solid 1px grey;
}
</style>
