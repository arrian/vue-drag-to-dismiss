<template>
  <div class="drag-to-dismiss" :style="{ transform: transformString, opacity: opacity }">
    <slot ref="content"></slot>
  </div>
</template>

<script>
export default {
  name: 'DragToDismiss',

  props: {
    dismissDistance: {
      type: Number,
      default: 100
    },
    dismissed: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      dragging: false,
      start: {
        x: 0,
        y: 0
      },
      translate: {
        x: 0,
        y: 0
      },
      stopTimestamp: null
    };
  },
  
  mounted() {
    this.$el.addEventListener('touchstart', e => this.handleStart(e.touches[0]), false);
    this.$el.addEventListener('touchend', e => this.handleEnd(e.touches[0]), false);
    this.$el.addEventListener('touchcancel', e => this.handleCancel(e.touches[0]), false);
    this.$el.addEventListener('touchmove', e => this.handleMove(e.touches[0]), false);

    this.$el.addEventListener('mousedown', e => this.handleStart(e), false);
    this.$el.addEventListener('mouseup', e => this.handleEnd(e), false);
    this.$el.addEventListener('mousemove', e => this.handleMove(e), false);
    this.$el.addEventListener('mouseout', e => this.handleCancel(e), false);

    window.addEventListener('mouseup', e => this.handleEnd(e), false);
  },

  methods: {
    handleStart(e) {
      e.preventDefault && e.preventDefault();
      this.dragging = true;
      this.start.x = e.clientX;
      this.start.y = e.clientY;
      this.translate.x = 0;
      this.translate.y = 0;
      console.log('start', e);
    },

    handleEnd(e) {
      this.dragging = false;
      this.start.x = null;
      this.start.y = null;

      if(this.distance > this.dismissDistance) {
        this.$emit('update:dismissed', true);
        this.translate.x = 0;
        this.translate.y = 0;
      } else {
        if(this.translate.x || this.translate.y) {
          window.requestAnimationFrame(this.animate);
        }
      }



      
      // this.translate.x = 0;
      // this.translate.y = 0;
      console.log('end', e);
    },

    handleCancel(e) {
      // this.dragging = false;
      // this.start.x = null;
      // this.start.y = null;
      // this.translate.x = 0;
      // this.translate.y = 0;
      console.log('cancel', e);
    },

    handleMove(e) {
      if(this.dragging) {
        console.log(this.translate.x, e.clientX, this.start.x);
        console.log(this.translate.y, e.clientY, this.start.y);
        this.translate.x = e.clientX - this.start.x;
        this.translate.y = e.clientY - this.start.y;
        console.log('move', e);
      }
    },

    animate(timestamp) {
      if(this.dragging) return;

      if(!this.stopTimestamp) this.stopTimestamp = timestamp;

      const delta = timestamp - this.stopTimestamp;
      console.log(delta);

      if(delta > 0) {
        const multiplier = 1 / (delta / 10);
        console.log(multiplier)

        this.translate.x *= multiplier;
        this.translate.y *= multiplier;

        if(Math.abs(this.translate.x) < 1) this.translate.x = 0;
        if(Math.abs(this.translate.y) < 1) this.translate.y = 0;
      }

      if(this.translate.x || this.translate.y) {
        window.requestAnimationFrame(this.animate);
      } else {
        this.stopTimestamp = null;
      }
    }
  },

  computed: {
    transformString() {
      return `translate(${this.translate.x}px, ${this.translate.y}px) rotate(${this.rotate}deg)`;
    },
    rotate() {
      return this.translate.x / 20;
    },
    distance() {
      const t = this.translate;
      return Math.sqrt(t.x * t.x + t.y * t.y);
    },
    opacity() {
      if(this.dismissed) {
        return 0;
      }
      return 1 - Math.min(this.distance / 200);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
