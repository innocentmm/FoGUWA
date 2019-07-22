<template>
  <google-map-loader
    :map-config="mapConfig"
    :api-key="apiKey"
    :map-inst.sync="map"
    :google-inst.sync="google"
  />
</template>

<script>
import GoogleMapLoader from './GoogleMapLoader'
import { uwaMapSettings } from '@/assets/js/mapSettings'
import { plants } from '@/assets/plantdb.json'

// Use https://www.gps-coordinates.net/ to easily fetch coordinates
const UWA_BOUNDS = {
  north: -31.972979992153224,
  south: -31.98713100347039,
  west: 115.81349721126503,
  east: 115.82249284467866
}
const UWA_COORDS = { lat: -31.9804624, lng: 115.818 }

export default {
  components: {
    GoogleMapLoader
  },
  data() {
    return {
      map: null,
      google: null,
      markerInstances: [],
      infowindow: null
    }
  },
  computed: {
    apiKey() {
      return process.env.googleMapsApi.toString()
    },
    mapConfig() {
      return {
        center: UWA_COORDS,
        restriction: {
          latLngBounds: UWA_BOUNDS,
          strictBounds: false
        },
        minZoom: 7,
        maxZoom: 21,
        zoom: 15,
        ...uwaMapSettings
      }
    },
    plants() {
      // Make copy the array of plants
      // TODO make prop, so this can be managed and filtered externally (e.g. by searchbar)
      return [...plants]
    }
  },
  watch: {
    // TODO these two watchers will be called quickly enough, such that both map and google are both defined
    map(val) {
      this.loadMarkers()
    },
    google(val) {
      this.loadMarkers()
    },
    plants(val) {
      this.loadMarkers()
    }
  },
  methods: {
    loadMarkers() {
      if (this.map && this.google) {
        // Clear old markers
        for (const marker of this.markerInstances) {
          marker.setMap(null)
        }
        this.markerInstances = []
        // Create new markers and store them
        const leafIcon = {
          path: 'M17 8C8 10 5.901 16.166 3.816 21.343l1.891.65.954-2.292c.482.168.976.299 1.339.299C19 20 22 3 22 3c-1 2-8 2.25-13 3.25S2 11.5 2 13.5s1.75 3.75 1.75 3.75C7 8 17 8 17 8z',
          fillColor: '#008000',
          fillOpacity: 1.0,
          strokeColor: '#004d00',
          scale: 1
        }
        const statueIcon = {
          path: 'M17 8C8 10 5.901 16.166 3.816 21.343l1.891.65.954-2.292c.482.168.976.299 1.339.299C19 20 22 3 22 3c-1 2-8 2.25-13 3.25S2 11.5 2 13.5s1.75 3.75 1.75 3.75C7 8 17 8 17 8z',
          fillColor: '#cd7f32',
          fillOpacity: 1.0,
          strokeColor: '#905923',
          scale: 1
        }
        this.plants.forEach((plant, index) => {
          // const leafIcon = {
          //   url: 'data:image/svg+xml;utf8,' + encodeURIComponent(this.$refs.leafIconSVG.outerHTML),
          //   size: null, // new this.google.maps.Size(24, 24),
          //   origin: null, // new this.google.maps.Point(0, 0),
          //   anchor: null, // new this.google.maps.Point(12, 12),
          //   scaledSize: new this.google.maps.Size(this.width, this.height)
          // }
          // Plot all instances
          console.log(plant) // eslint-disable-line
          const infowindow = new this.google.maps.InfoWindow({
            content: this.popUpStyle(plant),
            map: this.map
          })
          for (const instance of plant.instances) {
            const markerInst = new this.google.maps.Marker({
              label: plant.name,
              position: instance.location,
              icon: (plant.type === 'tree' ? leafIcon : statueIcon),
              map: this.map
            })
            markerInst.addListener('click', () => {
              infowindow.open(this.map, markerInst)
            })
            this.markerInstances.push(markerInst)
          }
        })
      }
    },
    popUpStyle(plant) {
      return `
      <div id="container">
      <b>${plant.name}</b>
      <i>${plant.scientificName}</i>
      ${plant.description}
      </div>`
    }
  }
}
</script>

<style>
#container {
margin-bottom: 10px;
width: 150px;
top: 15px;
left: 0px;
}
</style>
