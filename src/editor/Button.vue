<template lang="pug">
div(@mousedown="onBtnClick" @keydown="onKey")
	a(:class="'vw-btn-'+module.title", v-html="module.icon")

	.dashboard(
		v-show="showDashboard",
		ref="dashboard",
		@mousedown="onDashboardClick"
		style="display:flex; background:transparent; border:none; padding: 0"
	)
		component(
	    v-if="module.render",
	    v-once,
	    ref="moduleDashboard",
	    :is="module",
	    @exec="exec",
			@savesel="savesel",
			@restoresel="restoresel",
	    :uid="uid"
	    :options="options"
	  )

</template>
<script>
import bus from 'src/editor/bus.js';

export default {
	props: ["module", "options"],

	data () {
		return {
			showDashboard: false,
			mouseInModule: false,
		}
	},

  computed: {
    uid () {
      return this.$parent._uid
    },
		component() {
			return this.$refs.moduleDashboard
		}
  },

	methods: {
		onDashboardClick($event) {
			if (this.component.onDashboardClick && this.mouseInModule == false) {
				this.component.onDashboardClick()
			}
		},
		closeDashboard () {
			if (this.component.willClose) {
				this.component.willClose()
			}

			this.showDashboard = false;
		},
		openDashboard () {
			if (this.component.willOpen) {
				this.component.willOpen()
			}

			this.showDashboard = true;
		},

    exec () {
      this.$parent.exec.apply(null, arguments)
		},

		savesel () {
      this.$parent.saveSelection()
		},

		restoresel () {
      this.$parent.restoreSelection()
		},

		onKey ($event) {
			if (this.component.onKey) {
				this.component.onKey($event)
			}
		},

		onBtnClick ($event) {
			if($event.target.localName == 'input'){
				return;
			}
			$event.preventDefault();

			if (this.module.action !== undefined)
				this.exec.apply(null, this.module.action);

			else if (this.module.customAction !== undefined) {
				this.module.customAction(bus.utils).forEach(a => this.exec.apply(null, a));
			}

			else if (this.module.render !== undefined && (!this.$refs.dashboard || !this.$refs.dashboard.contains($event.target))) {
				if (this.showDashboard)
					this.closeDashboard()
				else
					this.openDashboard()

				bus.emit(`${this.uid}_${this.showDashboard ? "show" : "hide"}_dashboard_${this.module.title}`);
				return;
			}
		}
	}
}
</script>
