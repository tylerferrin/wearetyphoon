<template>
  <section class="container">
    <div class="moon-container" v-bind:class="{fadeIn: !play}"></div>
    <div class="video-container" v-if="play" v-bind:class="{fadeIn: play}">
      <iframe id="video" width="100%" height="100%" src="//www.youtube.com/embed/qUJYqhKZrwA?autoplay=1&showinfo=0&controls=0" frameborder="0" allowfullscreen />
    </div>
    <nav v-bind:class="{fadeIn: !play}">
      <a href="#" @click="playToggle()" v-if="!play" class="link playGlitch">PLAY</a>
      <a href="#" @click="playToggle()" v-if="play" class="link stopGlitch" v-bind:class="{fadeIn: play}">STOP</a>
      <a href="#" @click="listenUp()" class="link listenGlitch">LISTEN</a>
      <a href="http://typhoon.merchline.com/" class="link storeGlitch">STORE</a>
      <p @click="showHiddenLinks()" class="moreOptionsGlitch"> + </p>
    </nav>
    <div v-if="!hideHiddenLinks" class="navBottomBorderContainer">
      <div v-bind:class="{ navBottomBorder: !hideHiddenLinks}"></div>
    </div>
    <ul class="navExtender" v-bind:class="{ hideLinkDisplay: hideHiddenLinks, fastFadeIn: !hideHiddenLinks }">
      <li><a href="#">Twitter</a></li>
      <li><a href="#">Facebook</a></li>
      <li><a href="#">Instagram</a></li>
    </ul>
    <div class="showListContainer">
      <div class="showList" v-if="listen" v-bind:class="{ fastFadeIn: listen }">
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
              <td class="isMobile">{{ show.venue }}</td>
              <td>{{ show.city }}</td>
              <td class="tickets"><a :href="show.linkToPurchase"> Tickets </a></td>
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
      hideHiddenLinks: true,
      listenUp: () => {
        this.listen = !this.listen
        this.play = false
        this.hideHiddenLinks = true
      },
      playToggle: () => {
        this.play = !this.play
        this.listen = false
        this.hideHiddenLinks = true
      },
      showHiddenLinks: () => {
        this.hideHiddenLinks = !this.hideHiddenLinks
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
  height: 100vh
  background-color: black
  .moon-container
    height: 100vh
    width: 100vw
    position: absolute
    z-index: 4
    &:after
      content: ""
      background-image: url('~/assets/moon.gif')
      background-size: cover
      background-position: center
      opacity: .45
      position: absolute
      top: 0
      left: 0
      bottom: 0
      right: 0
      z-index: -1


.fadeIn
  animation-name: fadeIn
  animation-duration: 5s
  animation-timing-function: ease-in
  animation-fill-mode: forwards

.fastFadeIn
  animation-name: fadeIn
  animation-duration: 2.5s
  animation-timing-function: ease-in
  animation-fill-mode: forwards
nav
  position: absolute
  top: 88.5vh
  right: 9.5vw
  display: flex
  flex-direction: row
  justify-content: flex-end
  z-index: 6
  padding-bottom: 10px
  @media screen and (max-width: 768px)
    right: 0
    margin: 0 auto
  a
    padding: 0 25px
    font-size: 14px
    letter-spacing: 2px
    color: white
    text-decoration: none
    &:hover
      color: white
      text-decoration: none
    @media screen and (max-width: 768px)
      font-size: 13px
      letter-spacing: 1px
  p
    color: white
    padding: 0 25px


.navExtender
  padding: 0
  margin: 0
  color: white
  display: inline-block
  list-style-type: none
  position: absolute
  top: 92.5vh
  right: 9.5vw
  z-index: 10
  font-size: 12px
  @media screen and (max-width: 768px)
    right: 0
li
  position: relative
  top: 15px
  padding: 0 0 0 25px
  display: inline-block
  float: right
  a
    color: white
    text-decoration: none
    &:hover, &:focus
      color: white
      text-decoration: none
li:first-of-type
  padding-right: 25px
p
  display: inline-block
  position: relative
  top: -3px
  margin: 0
  &:hover
    cursor: pointer

.hideLinkDisplay
  display: none

.navBottomBorderContainer
  position: absolute
  top: 93vh
  right: 9.5vw
  height: 5px
  width: 320px
  color: white
  z-index: 9
  @media screen and (max-width: 768px)
    right: 0

.navBottomBorder
  position: relative
  top: -8px
  right: 25px
  height: 2px
  background: white
  animation-name: slideOut
  animation-duration: .5s
  animation-timing-function: ease-in
  animation-direction: forwards

@keyframes slideOut
  0%
    width: 0px
  100%
    width: 320px


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
  // align-items: center
  justify-content: center
  .showList
    display: flex
    flex-direction: row
    justify-content: center
    position: absolute
    z-index: 50
    color: #DAA520
    width: 100vw
    table
      border-collapse: collapse
      margin: 0 05vw 0 15vw
    tr
      height: 75px
    td
      text-align: left
      width: 30vw
      font-family: 'Times New Roman', serif
      font-size: 20px
      font-weight: bold
      -webkit-font-smoothing: antialiased
      @media screen and (max-width: 1200px)
        font-size: 14px
      @media screen and (max-width: 768px)
        font-size: 12px
    a
      color: #DAA520
      text-decoration: none
      &:hover
        color: #DAA520
      &:focus
        outline: none

@media screen and (max-width: 600px)
  td
    display: block
    text-align: center
    font-size: 24px !important
    width: 50vw !important
  td.tickets
    margin-bottom: 24px
  .isMobile
    display: none

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


/// GLITCHY STUFFS

@mixin glitchEffects($string)
  margin: 0
  text-decoration: none
  color: #fff
  &:before, &:after
    position: absolute
    display: inline-block
    content: $string
    opacity: .8
    display: none
    @media screen and (max-width: 768px)
      display: none
  &:after
    color: red
    z-index: -2
    left: 25px
  &:before
    color: yellow
    z-index: -1
    top: 1px
  &:hover
    &:before
      display: block
      animation: glitch .3s cubic-bezier(.25, .46, .45, .94) both infinite
    &:after
      display: inline-block
      animation: glitch .3s cubic-bezier(.25, .46, .45, .94) reverse both infinite

@keyframes glitch
  0%
    transform: translate(0)
    color: lighten(red, 20%)
  20%
    transform: translate(-1.5px, 1.5px)
    color: pink
  40%
    transform: translate(-1.5px, -1.5px)
    color: lighten(green, 30%)
  60%
    transform: translate(1.5px, 1.5px)
    color: lighten(purple, 30%)
  80%
    transform: translate(1.5px, -1.5px)
    color: lighten(blue, 30%)
  to
    transform: translate(0)
    color: orange

/// NAV GLITCH CLASSES
.playGlitch
  @include glitchEffects('PLAY')

.stopGlitch
  @include glitchEffects('STOP')

.listenGlitch
  @include glitchEffects('LISTEN')
  &:after
    left: 118.5px

.storeGlitch
  @include glitchEffects('STORE')
  &:after
    left: 228.5px

.moreOptionsGlitch
  @include glitchEffects('+')
  &:after
    left: 25px
    bottom: 0px

.xGlitch
  @include glitchEffects('X')

</style>
