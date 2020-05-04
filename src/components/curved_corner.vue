<template>
  <div>
    <svg version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' :viewBox="'0 0 ' + svgwidth + ' ' + svgheight" class="mask-def"> 
    <defs>
    <clipPath :id="id"><path :d="'M0,0\
    H '+(curvature)+'\
    C '+(curvature/5)+', 0, 0, '+(curvature/5)+', 0, '+(curvature)+' \
    V '+(svgheight - curvature)+' \
    c 0, '+(curvature/1.25)+', '+(curvature/5)+', '+(curvature)+', '+(curvature)+', '+(curvature)+'\
    H '+(svgwidth - curvature)+' \
    c '+(curvature/1.25)+', 0, '+(curvature)+', '+(-(curvature/5))+', '+(curvature)+','+(-curvature)+'\
    V '+(curvature)+'\
    C '+(svgwidth)+', '+(curvature/5)+', '+(svgwidth-(curvature/5))+', 0, '+(svgwidth - curvature)+', 0\
    Z'"></path></clipPath></defs></svg>
    <div class="curved-corner-wrapper"  :style="'clip-path: url(#' + id + '); -webkit-clip-path: url(#' + id + ');'">
      <div class="content">
        <slot></slot>  
      </div>
    </div>
  </div>  
</template>

<script>
import _ from 'lodash'

export default {
  name: 'curved-corner',
  props: ['trycurvature'],
  data: function() {
    return{
      svgwidth: 0,
      svgheight: 0,
      id: null
    }
  },
  created() {
    this.id = `mask${this._uid}`
  },
  mounted() {
    // only way to get the stupid thing to actually get the correct dimensions
    setTimeout(()=>{ this.svgResize() }, 75);

    window.addEventListener('resize', _.debounce(() => {``
      this.svgResize()
    }, 50))
  
  },
  updated() {
    this.svgResize()
  },
  computed: {
    curvature: function() {
      if (window.matchMedia("(max-width: 812px)").matches) {
        return 15
      } else {
        return this.trycurvature
      }
    }
  },
  methods: {
    svgResize() {
      this.svgwidth = this.$el.clientWidth
      this.svgheight = this.$el.clientHeight
    }      
  }, 

}
</script>

