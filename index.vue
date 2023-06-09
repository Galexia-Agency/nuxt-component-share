<style>
.share-button-group {
  display: flex;
  flex-wrap: wrap;
  column-gap: .5rem;
  row-gap: 1rem
}
</style>

<template>
  <div class="share">
    <h4 v-if="titleText" class="share-title" v-text="titleText" />
    <template v-if="!nativeShareEnabled">
      <div class="share-button-group">
        <ShareNetwork
          v-for="social in socials"
          :key="social.socialShareNetwork"
          :network="social.socialShareNetwork"
          :tag="tag"
          :popup="popup"
          :url="url"
          :title="title"
          :description="description"
          :quote="quote"
          :hashtags="hashtags"
          :twitter-user="twitterUser"
          :media="media"
          class="share-button share-button--social"
          @open="open"
          @change="change"
          @close="close"
        >
          <FontAwesomeIcon v-if="hasLoadedIcons" :icon="['fab', social.fontAwesomeIcon.toLowerCase()]" /> {{ social.socialShareNetwork }}
        </ShareNetwork>
      </div>
    </template>
    <template v-else>
      <a class="share-button share-button--native" href="javascript:void(0)" @click="nativeShare">
        <FontAwesomeIcon :icon="['fas', 'share-alt']" /> {{ buttonText }}
      </a>
    </template>
  </div>
</template>

<script>
import { ShareNetwork } from 'vue-social-sharing'
import { library } from '@fortawesome/fontawesome-svg-core'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faShareAlt } from '@fortawesome/free-solid-svg-icons'

library.add(faShareAlt)

export default {
  name: 'GalexiaNuxtComponentShare',
  components: {
    ShareNetwork,
    FontAwesomeIcon
  },
  props: {
    socials: {
      type: Array,
      default: () => {
        return [
          { socialShareNetwork: 'Facebook', fontAwesomeIcon: 'Facebook' },
          { socialShareNetwork: 'LinkedIn', fontAwesomeIcon: 'Linkedin' },
          { socialShareNetwork: 'Twitter', fontAwesomeIcon: 'Twitter' },
          { socialShareNetwork: 'WhatsApp', fontAwesomeIcon: 'Whatsapp' }
        ]
      }
    },
    tag: {
      type: String,
      default: 'a'
    },
    popup: {
      type: Object,
      default: () => {
        return {
          width: 626,
          height: 436
        }
      }
    },
    title: {
      type: String,
      required: true
    },
    description: {
      type: String,
      required: true
    },
    quote: {
      type: String,
      default: ''
    },
    hashtags: {
      type: String,
      default: ''
    },
    twitterUser: {
      type: String,
      default: ''
    },
    media: {
      type: String,
      default: ''
    },
    titleText: {
      type: [String, Boolean],
      default: () => {
        return 'Share:'
      }
    },
    buttonText: {
      type: String,
      default: 'Share this'
    }
  },
  data () {
    return {
      hasLoadedIcons: false
    }
  },
  computed: {
    shareObject () {
      return {
        title: this.$nuxt.$options.head.titleTemplate.replace('%s', this.title),
        text: this.description.replaceAll('<p>', '').replaceAll('</p>', '').replaceAll('[&hellip;]', '').replaceAll('\n', ''),
        url: this.url
      }
    },
    url () {
      return process.client ? window.location.origin + this.$nuxt.$route.fullPath : ''
    },
    nativeShareEnabled () {
      if (!process.client) {
        return false
      }
      try {
        return navigator.canShare(this.shareObject)
      } catch (e) {
        return false
      }
    }
  },
  created () {
    const importPromises = this.socials.map((social) =>
      import(`@fortawesome/free-brands-svg-icons/fa${social.fontAwesomeIcon}.js`).then(
        (module) => {
          library.add(module[`fa${social.fontAwesomeIcon}`])
        }
      )
    )
    Promise.all(importPromises).then(() => {
      this.hasLoadedIcons = true
    })
  },
  methods: {
    nativeShare () {
      navigator.share(this.shareObject)
    },
    open (e) {
      this.$emit('open', e)
    },
    change (e) {
      this.$emit('change', e)
    },
    close (e) {
      this.$emit('close', e)
    }
  }
}
</script>
