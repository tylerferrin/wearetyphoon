<template>
  <section class="container">
    <div class="moon-container">
      <div class="video-container" v-if="play">
        <div class="closeVideo" @click="play = !play">X</div>
        <iframe id="video" width="100%" height="100%" src="//www.youtube.com/embed/qUJYqhKZrwA?autoplay=1&showinfo=0&controls=1" frameborder="0" allowfullscreen />
      </div>
    </div>
    <nav v-if="!play">
      <a href="#" @click="play = !play" class="link">PLAY</a>
      <a href="#" class="link">LISTEN</a>
      <a href="#" class="link">STORE</a>
    </nav>
    <div class="showList">
      <table>
        <tr>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
        </tr>
        <tr v-for="show in shows">
          <td>{{ show.date }}</td>
          <td>{{ show.venue }}</td>
          <td>{{ show.city }}</td>
          <td><a :href="show.linkToPurchase"> TICKETS </a></td>
        </tr>
      </table>
    </div>
  </section>
</template>

<script>
import * as contentful from 'contentful'
import _ from 'lodash'
import moment from 'moment'

export default {
  data () {
    return {
      play: false
    }
  },
  asyncData (context) {
    const client = contentful.createClient({
      space: process.env.spaceId,
      accessToken: process.env.accessToken
    })
    return client.getEntries()
      .then((response) => {
        let filteredDownResponse = _.map(response.items, (item) => {
          return Object.assign({}, item.fields, item.sys.contentType.sys)
        })
        filteredDownResponse = _.each(filteredDownResponse, (item) => {
          item.date = moment(item.date).format('MM.DD.YYYY')
        })
        return {
          shows: _.orderBy(_.filter(filteredDownResponse, (item) => {
            return item.id === 'show'
          }), 'date'),
          videos: _.filter(filteredDownResponse, (item) => {
            return item.id === 'video'
          })
        }
      })
      .catch(console.error)
  }
}
</script>

<style lang="sass">
body
  font-family: 'Times New Roman'
  background-color: black
  letter-spacing: 1px

.container
  min-height: 100vh
  background-color: black
  .moon-container
    background-image: url('~/assets/moon.gif')
    background-size: cover
    background-position: center
    min-height: 100vh
    min-width: 100vw
    animation-name: fadeIn
    animation-duration: 30s
    position: absolute

nav
  position: relative
  top: 92.5vh
  right: 7.5vw
  display: flex
  flex-direction: row
  justify-content: flex-end
  a
    padding: 0 25px
    font-size: 12px
    color: white
    text-decoration: none
    &:hover
      color: white
      text-decoration: none

@keyframes fadeIn
  from
    opacity: 0
  to
    opacity: 1

.isPlaying
  display: none
  transition: 5s

.showList
  position: absolute
  z-index: 50
  color: red
  table
    border-collapse: collapse
    margin: 25px
  tr
    border-bottom: 1px solid white
    height: 50px
  td
    text-align: right
    width: 25vw
  a
    color: red
    text-decoration: none
    &:hover
      color: red

#video
  height: 100vh
  width: 100vw

.closeVideo
  position: absolute
  top: 5vh
  right: 5vw
  color: white
  font-size: 12px
  &:hover
    cursor: pointer

</style>
