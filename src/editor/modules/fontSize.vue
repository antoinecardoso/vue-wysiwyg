<template>
	<div style="background: #f6f6f6; border-bottom: 1px solid #e4e4e4; width: 100%; padding: 8px 16px">
		<span>Taille du texte :</span>
	  <input ref="size" v-model="size" @keyup.enter="changeSize"/> en px
	</div>
</template>

<script>
import bus from 'src/editor/bus.js';

export default {

  data() {
    return {
      size: 16,
      sel: null,
    }
  },
  title: "fontsize",
  icon: '<svg width="1792" height="1792" viewBox="0 -45 464.736 464.736" style="enable-background:new 0 0 464.736 464.736;" xmlns="http://www.w3.org/2000/svg"><g>	<g>		<path d="M277.588,74.292c1.898,2.363,4.766,3.737,7.797,3.737h22.588c2.66,0,5.211-1.061,7.088-2.946			c1.877-1.885,2.926-4.441,2.912-7.102l0.035-55.064c-0.064-5.479-4.523-9.888-10-9.888H10c-5.523,0-10,4.478-10,10v55			c0,5.522,4.477,10,10,10h22.596c3.039,0,5.912-1.381,7.809-3.753L57.403,53.03h71.592v259.635c0,5.521,4.477,10,10,10h40			c5.521,0,10-4.479,10-10V53.031h71.513L277.588,74.292z"/>		<path d="M323.617,260.448c-2.627-3.409-7.143-4.754-11.207-3.342l-12.756,4.437l0.002-38.723c0-2.652-1.053-5.196-2.928-7.072			c-1.875-1.875-4.42-2.929-7.072-2.929h-9.99c-5.521,0-10,4.479-10,10v38.724l-12.758-4.437c-4.062-1.416-8.576-0.067-11.203,3.342			c-2.629,3.408-2.779,8.115-0.375,11.688l31.033,46.113c1.859,2.762,4.971,4.418,8.297,4.418c3.328,0,6.439-1.656,8.297-4.418			l31.035-46.113C326.395,268.563,326.244,263.856,323.617,260.448z"/>	</g></g></svg>',
  props: {
    uid: null
  },
  created() {
    bus.on(this.uid + "_show_dashboard_fontsize", () => {

      this.size = 16
      this.$refs.size.focus();

			if (document.getSelection().rangeCount != 0) {
      	this.sel = document.getSelection().getRangeAt(0);
			} else {
				this.sel = null
			}
    });
  },
  methods: {
		changeSize() {
			var size = this.size
			var sel = this.sel

			let selection = window.getSelection();
			selection.removeAllRanges();
			selection.addRange(this.sel);

			sel = window.getSelection();

			var selectedHtml = "";
			if (sel.rangeCount) {
				var container = document.createElement("div");
				for (var i = 0, len = sel.rangeCount; i < len; ++i) {
					container.appendChild(sel.getRangeAt(i).cloneContents());
				}
				selectedHtml = container.innerHTML;
			}

			let html = `<div style="font-size: ${size}px;">${selectedHtml}</div>`

			this.$emit("exec", 'insertHTML', html);
			this.$parent.closeDashboard();
		},
  }
};
</script>
