<template>
  <section class="container">
    <div class="moon-container" v-bind:class="{fadeIn: !play}"></div>
    <div class="video-container" v-if="play" v-bind:class="{fadeIn: play}">
      <iframe id="video" width="100%" height="100%" src="https://www.youtube.com/embed/Og0inPrtfP4?rel=0&amp;controls=0&amp;showinfo=0&autoplay=1" frameborder="0" allowfullscreen></iframe>
    </div>
    <nav v-bind:class="{fadeIn: !play}" v-if="!listen">
      <a href="#" @click="playToggle()" class="link listenGlitch">LISTEN</a>
      <a href="#" @click="listenUp()" class="link attendGlitch">ATTEND</a>
      <a href="http://typhoon.merchline.com/" target="_blank" class="link storeGlitch">STORE</a>
    </nav>
    <nav class="datesNav" v-bind:class="{fadeIn: !play}" v-if="listen">
      <a href="#" @click="playToggle()" class="link listenGlitch">LISTEN</a>
      <a href="#" @click="listenUp()" class="link attendGlitch">ATTEND</a>
      <a href="http://typhoon.merchline.com/" target="_blank" class="link storeGlitch">STORE</a>
    </nav>
    <div class="showListContainer" v-if="listen">
      <div class="showList" v-bind:class="{ fastFadeIn: listen }">
        <div class="showRow" v-for="show in shows">
          <p>{{ show.date }}</p>
          <p>{{ show.venue }}</p>
          <p>{{ show.city }}</p>
          <p class="tickets"><a :href="show.linkToPurchase" target="_blank"> Tickets </a></p>
        </div>
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
      background-image: url('~/assets/moon_small.gif')
      background-size: 500px
      background-repeat: no-repeat
      background-position: center
      opacity: .45
      position: fixed
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
  position: fixed
  top: 88.5vh
  right: 9.5vw
  display: flex
  flex-direction: row
  justify-content: flex-end
  z-index: 100
  padding-bottom: 10px
  @media screen and (orientation: landscape) and (max-width: 600px)
    top: 7.5vh

  @media screen and (max-width: 600px)
    top: 5vh
    display: flex
    flex-direction: row
    justify-content: center
    width: 100vw
    right: 0
  p
    color: white
    padding: 0 25px
  a
    padding: 0 25px
    font-size: 14px
    letter-spacing: 2px
    color: white
    text-decoration: none
    &:hover
      color: white
      text-decoration: none

.datesNav
  position: fixed
  top: 10.5vh
  right: 9.5vw
  display: flex
  flex-direction: row
  justify-content: flex-end
  z-index: 100
  padding-bottom: 10px
  @media screen and (orientation: landscape) and (max-width: 600px)
    top: 7.5vh

  @media screen and (max-width: 600px)
    top: 5vh
    display: flex
    flex-direction: row
    justify-content: center
    width: 100vw
    right: 0
  p
    color: white
    padding: 0 25px
  a
    padding: 0 25px
    font-size: 14px
    letter-spacing: 2px
    color: white
    text-decoration: none
    &:hover
      color: white
      text-decoration: none




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
  height: 100vh
  width: 100vw
  display: flex
  flex-direction: column
  justify-content: center
  z-index: 10
  margin-bottom: 200px
  @media (max-width: 600px)
    height: auto
  .showList
    display: flex
    flex-direction: column
    justify-content: center
    height: auto
    overflow-y: scroll
    position: relative
    .showRow
      display: flex
      flex-direction: row
      justify-content: space-around
      position: relative
      top: 125px
      margin: 20px 0
      @media screen and (max-width: 600px)
        position: relative
        top: 100px
        flex-direction: column
        justify-content: flex-start
        align-items: center
        margin: 25px 0
        p
          margin: 0
      p
        text-align: center
        width: 18vw
        color: darken(gold, 5%)
        display: inline-block
        font-size: 16px
        @media screen and (max-width: 768px)
          font-size: 14px
        @media screen and (max-width: 600px)
          width: 50vh
        a
          color: darken(gold, 5%)
          text-decoration: none
          &:hover
            color: inherit

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
      display: inline-block
      animation: glitch .3s cubic-bezier(.25, .46, .45, .94) both infinite
      @media screen and (max-width: 768px)
        display: none
    &:after
      display: inline-block
      animation: glitch .3s cubic-bezier(.25, .46, .45, .94) reverse both infinite
      @media screen and (max-width: 768px)
        display: none

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
.listenGlitch
  @include glitchEffects('LISTEN')

.stopGlitch
  @include glitchEffects('STOP')

.attendGlitch
  @include glitchEffects('ATTEND')
  &:after
    left: 135.5px

.storeGlitch
  @include glitchEffects('STORE')
  &:after
    left: 250.5px

.moreOptionsGlitch
  @include glitchEffects('+')
  &:after
    left: 25px
    bottom: 0px
  @media screen and (max-width: 600px)
    display: none

p.moreOptionsGlitch
  @media (orientation: landscape) and (max-width: 600px)
    display: none !important

.xGlitch
  @include glitchEffects('X')

</style>
