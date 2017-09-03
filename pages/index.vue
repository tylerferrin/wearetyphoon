<template>
  <section class="container">
    <div class="moon-container" v-bind:class="{fadeIn: !play}"></div>
    <div class="video-container" v-if="play" v-bind:class="{fadeIn: play}">
      <div class="closeVideo" @click="play = !play">X</div>
      <iframe id="video" width="100%" height="100%" src="//www.youtube.com/embed/qUJYqhKZrwA?autoplay=1&showinfo=0&controls=0" frameborder="0" allowfullscreen />
    </div>
    <nav v-bind:class="{addBorder: showingHiddenLinks}">
      <a href="#" @click="playToggle()" v-if="!play" class="link">PLAY</a>
      <a href="#" @click="playToggle()" v-if="play" class="link">STOP</a>
      <a href="#" @click="listenUp()" class="link">LISTEN</a>
      <a href="http://typhoon.merchline.com/" class="link">STORE</a>
      <p @click="showHiddenLinks()"> + </p>
    </nav>
    <ul class="navExtender" v-bind:class="{ linkDisplay: !showingHiddenLinks }">
      <li>a</li>
      <li>B</li>
      <li>C</li>
      <li>D</li>
    </ul>
    <div class="showListContainer">
      <div class="showList" v-if="listen" v-bind:class="{ fadeIn: listen }">
        <table>
          <tbody>
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
              <td><a :href="show.linkToPurchase" class="tickets"> Tickets </a></td>
            </tr>
          </tbody>
        </table>
      </div>
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
      play: false,
      listen: false,
      showingHiddenLinks: false,
      listenUp: () => {
        this.play = false
        this.listen = !this.listen
      },
      playToggle: () => {
        this.play = !this.play
        this.listen = false
      },
      showHiddenLinks: () => {
        this.showingHiddenLinks = !this.showingHiddenLinks
      }
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
  background-color: black
  letter-spacing: 2px
  margin: 0

.container
  min-height: 100vh
  background-color: black
  .moon-container
    background-image: url('~/assets/moon.gif')
    background-size: cover
    background-position: center
    min-height: 100vh
    min-width: 100vw
    position: absolute
    z-index: 4

.fadeIn
  animation-name: fadeIn
  animation-duration: 5s
  animation-timing-function: ease-in-out
  animation-fill-mode: forwards

nav
  position: absolute
  top: 90.5vh
  right: 9.5vw
  display: flex
  flex-direction: row
  justify-content: flex-end
  z-index: 6
  padding-bottom: 10px
  a
    padding: 0 25px
    font-size: 12px
    color: white
    text-decoration: none
    &:hover
      color: white
      text-decoration: none
  p
    color: white
    padding: 0 25px

.addBorder
  border-bottom: 2px solid white

.navExtender
  padding: 0
  margin: 0
  color: white
  display: inline-block
  list-style-type: none
  position: absolute
  top: 94.5vh
  right: 9.5vw
  z-index: 6
li
  position: relative
  top: 15px
  padding: 0 25px
  display: inline-block
  float: right
li:first-of-type
  padding-right: 25px
p
  display: inline-block
  position: relative
  top: -3px
  margin: 0
  &:hover
    cursor: pointer

.linkDisplay
  display: none


@keyframes fadeIn
  0%
    opacity: 0
  100%
    opacity: 1

.showListContainer
  position: absolute
  top: -14px
  height: 100vh
  width: 100vw
  display: flex
  flex-direction: column
  align-items: center
  justify-content: center
  .showList
    display: flex
    flex-direction: row
    justify-content: center
    position: absolute
    z-index: 50
    color: red
    table
      border-collapse: collapse
      margin: 0 05vw 0 15vw
    tr
      height: 50px
    td
      text-align: left
      width: 20vw
      font-family: 'Times New Roman', serif
      font-size: 18px
      -webkit-font-smoothing: antialiased
    td a.tickets
      text-align: right
    a
      color: red
      text-decoration: none
      &:hover
        color: red

.video-container
  height: 100vh
  width: 100vw
  position: absolute
  z-index: 5

.closeVideo
  position: absolute
  top: 5vh
  right: 5vw
  color: white
  font-size: 18px
  &:hover
    cursor: pointer

</style>
