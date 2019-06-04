<template>
<div @mouseover="over = true" @mouseleave="over = false" style="padding: 8px 16px">
  <color-picker v-if="$parent.showDashboard" ref="picker" theme="light" :color="color" :sucker-hide="true" :colors-default="[]" @changeColor="changeColor" />
</div>
</template>

<script>
import colorPicker from '@caohenghu/vue-colorpicker'

var selectionParent = function () {
    var parent = null, sel;

    if (window.getSelection) {
        sel = window.getSelection()
        if (sel.rangeCount) {
            parent = sel.getRangeAt(0).commonAncestorContainer
            if (parent.nodeType != 1) {
                parent = parent.parentNode
            }
        }
    } else if ( (sel = document.selection) && sel.type != "Control") {
        parent = sel.createRange().parentElement()
    }

    return parent
}

export default {
  components: {
    colorPicker
  },
  data() {
    return {
      color: '#8E8E93',
			colorHasChanged: false,
			over: false,
    }
  },
  title: "Color Picker",
  icon: '<svg width="1792" height="1792" viewBox="0 0 464.736 464.736" style="enable-background:new 0 0 464.736 464.736;" xmlns="http://www.w3.org/2000/svg"><g><path d="M446.598,18.143c-24.183-24.184-63.393-24.191-87.592-0.008l-16.717,16.717c-8.98-8.979-23.525-8.979-32.504,0c-8.981,8.972-8.981,23.533,0,32.505l5.416,5.419L134.613,253.377h-0.016l-62.685,62.691c-4.982,4.982-7.919,11.646-8.235,18.684l-0.15,3.344c0,0.016,0,0.03,0,0.046l-2.529,56.704c-0.104,2.633,0.883,5.185,2.739,7.048c1.751,1.759,4.145,2.738,6.63,2.738c0.135,0,0.269,0,0.42-0.008l30.064-1.331h0.016l18.318-0.815l8.318-0.366c9.203-0.412,17.944-4.259,24.469-10.776l240.898-240.891l4.506,4.505c4.49,4.488,10.372,6.733,16.252,6.733c5.881,0,11.764-2.245,16.253-6.733c8.98-8.973,8.98-23.534,0-32.505l16.716-16.718C470.782,81.544,470.782,42.334,446.598,18.143z M272.639,227.33l-84.6,15.96l137.998-138.004l34.332,34.316L272.639,227.33z"/><path d="M64.5,423.872c-35.617,0-64.5,9.145-64.5,20.435c0,11.284,28.883,20.428,64.5,20.428s64.486-9.143,64.486-20.428C128.986,433.016,100.117,423.872,64.5,423.872z"/></g></svg>',

  methods: {
    changeColor(color) {
			this.colorHasChanged = true
    },
		willClose() {
			if (this.colorHasChanged) {
				this.$emit("exec", 'foreColor', this.$refs.picker.colorSelected)
			}
			this.colorHasChanged = false
		},
		willOpen() {
			let parent = selectionParent()

			if (parent) {
				console.log(parent);
				while (parent.color == null && parent.parentNode) {
					parent = parent.parentNode
				}

				if (parent.color) {
					this.color = parent.color
					return
				}
			}

			this.color = '#8E8E93'
		},
		onDashboardClick() {
			if (this.over) {
				return
			}
			this.$parent.closeDashboard();
		},
		onKey($event) {
			if ($event.keyCode === 13) {
				this.$parent.closeDashboard();
				$event.preventDefault();
    	}
		},
  }
};
</script>
<style>
.hu-color-picker {
  width: 218px !important;
}
</style>
