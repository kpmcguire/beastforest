<template lang="pug">
  .frame
    .loading-indicator(:class="{hidden: this.loaded}")
    main
      header.wood-texture.vertically-centered.logo-wrapper
        h1.beastforest-logo Beast Forest
        p Caption Maker
      section
        .beastforest-container(id="beastforest-container" :class="[container_class, container_color_class, container_vertical_align_class]" :style="'background-size: '+ background_size +'px'")
          .background-image-nook(v-if="!is_pattern && !myBackground")
          .background-image(v-if="myBackground" :style="'background-image:url('+myBackground+')'")
          include assets/images/blob.svg
          .speech-bubble-wrapper
            .name(style="clip-path: url(#bean)")
              p {{speakers_name}}
            .speech-bubble(style="clip-path: url(#bread)")
              .content
                p {{contents}}
      footer.wood-texture.vertically-centered.footer-container
        small 
          | A DIY project from 
          a(href="https://kpmcguire.github.io") Kevin McGuire
    aside.sidebar.leaf-texture
      section.padding
        h1 Options

        curved-corner.button.small(curvature="25" v-if="!is_pattern")
          button(@click="togglePatternOrImage()") Use Pattern
        curved-corner.button.small(curvature="25" v-if="is_pattern")
          button(@click="togglePatternOrImage()" v-if="is_pattern") Use Image
        
        label.file-input-wrapper(v-if="!is_pattern")
          .button Upload
          .text {{uploaded_file_name}}
          input(type="file" @change='uploadImage')
        curved-corner.white-corners(curvature="50" v-if="is_pattern")
          h2 Pattern Color
          ul.choice-list
            li 
              a(@click="changeColor('section-green')" v-html="this.splitText('Green')")
            li 
              a(@click="changeColor('section-brown')" v-html="this.splitText('Brown')") 
              li 
              a(@click="changeColor('section-blue')" v-html="this.splitText('Blue')")
        curved-corner.white-corners(curvature="50" v-if="is_pattern")
          h2 Pattern Content
          ul.choice-list
            li 
              a(@click="changeContainer('bg-leaf')" v-html="this.splitText('Leaves')") 
            li 
              a(@click="changeContainer('bg-nook-miles')" v-html="this.splitText('Raccoon Ticket')") 
            li 
              a(@click="changeContainer('bg-coin')" v-html="this.splitText('Coin')")  
            li 
              h3 Pattern Spacing
              input(type="range" min="50" max="1000" step="10" v-model="background_size") 
        curved-corner.white-corners(curvature="50")
          h2 Caption Content
          label
            div Name
            input(type="text" v-model="speakers_name") 
          label
            div Contents
            textarea(v-model="contents" ) 
        curved-corner.white-corners(curvature="50")
          h2 Caption Position
          ul.choice-list
            li 
              a(@click="changeContainerAlign('top')" v-html="this.splitText('Top')") 
            li 
              a(@click="changeContainerAlign('centered')" v-html="this.splitText('Centered')") 
            li 
              a(@click="changeContainerAlign('bottom')" v-html="this.splitText('Bottom')" ) 
        curved-corner.button.big(curvature="45")
          button(@click="makeImage('frame')") Export Image


</template>

<script>
  import curved_corner from './components/curved_corner'
  import domtoimage from 'dom-to-image'
  export default {
    data() {
      return {
        container_class: 'bg-leaf',
        container_color_class: 'section-blue',
        container_vertical_align_class: 'bottom',
        speakers_name: 'Celeste',
        background_size: '200',
        contents: "It seems there's a magic wand that, if you make a wish and give it a wave, lets you become a whole new you!",
        is_pattern: true,
        output: null,
        myBackground: null,
        uploaded_file_name: null,
        loaded: false
      }
    },
    components: {
      'curved-corner': curved_corner
    },

    computed: {

    },

    created() {

    },

    mounted() {
      this.loaded = true
    },

    methods: {
      changeColor(color) {
        this.container_color_class = color
      },
      changeContainer(container) {
        this.container_class = container
      },
      changeContainerAlign(align) {
        this.container_vertical_align_class = align
      },
      togglePatternOrImage(){
        this.is_pattern = !this.is_pattern
        if (this.is_pattern) {
          this.myBackground = null
          this.uploaded_file_name = null
          this.container_class = 'bg-leaf'
          this.container_color_class = 'section-blue'
        }
      },
      makeImage() {
        this.loaded = false
        var node = document.getElementById('beastforest-container');
        const scale = 3;

        domtoimage.toJpeg(node, {
          quality: 0.95,
          height: node.offsetHeight * scale, 
          width: node.offsetWidth * scale,
          style: {
            transform: "scale(" + scale + ")", 
            transformOrigin: "top left", 
            width: node.offsetWidth + "px", 
            height: node.offsetHeight + "px"
          }
        })
        .then((dataUrl) => {
          let timestamp = ''
          const now = new Date()

          timestamp = now.getFullYear().toString()
          timestamp += `${now.getMonth()+1}`.toString()
          timestamp += now.getDate().toString()
          timestamp += now.getHours().toString()
          timestamp += now.getMinutes().toString()
          timestamp += now.getSeconds().toString()

          var link = document.createElement('a');
          link.download = `beast-forest-${timestamp}.jpg`;
          link.href = dataUrl;
          this.loaded = true
          link.click();
        })
        .catch(function (error) {
          console.error('oops, something went wrong!', error);
        }); 
      },
      uploadImage(e){
        const image = e.target.files[0]
        const reader = new FileReader()
        reader.readAsDataURL(image)
        this.uploaded_file_name = image.name
        this.container_class = null
        this.container_color_class = 'section-white'
        reader.onload = e =>{
          this.myBackground = e.target.result          
        }
      },
      splitText(text) {
        let tmp2 = text.split('')
        let tmp1 = ''
        tmp2.forEach((letter, i)=>{
          if (letter === ' ') {
            letter = '&nbsp;' 
          }
          tmp1 += `<span class="letter" aria-hidden="true" style="--char-index: ${i}">${letter}</span>`
        })
        text = `<span class="split-word" aria-label="${text}">${tmp1}</span>`
        return text
      }
    }
  }

  
</script>


<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}

.split-word .letter {
  position: relative;
  display: inline-block;
  transform: translateY(0) scale(1);
  transform-origin: bottom center;
}

.choice-list a:hover .split-word .letter{
  animation: slide-in 0.75s;
  animation-delay: calc(120ms * var(--char-index));
}

@keyframes slide-in {
  0% {
    transform: translateY(0) scale(1);
  }
  25% {
    transform: translateY(0.01em) scaleY(0.95);
  }
  75% {
    transform: translateY(-0.01em) scaleY(1.05);
  }
  100% {
    transform: translateY(0) scale(1);
  }
}
</style>
