<template>
<svg class="mbcofsoe" viewBox="0 0 10 10" preserveAspectRatio="none">

<circle 
	cx="5"
	cy="5"
	r="5"
	:fill="backgroundColor"
/>

	<circle v-for="(angle, i) in graduations"
					:key="i"
					:cx="5 + (Math.sin(angle) * (5 - graduationsPadding))"
					:cy="5 - (Math.cos(angle) * (5 - graduationsPadding))"
					:r="i % 5 == 0 ? 0.125 : 0.05"
					:fill="i % 5 == 0 ? majorGraduationColor : minorGraduationColor"
	/>

  <g>
    <rect
      x="8" 
      y="4.9" 
      width="1" 
      height="0.2" 
      style="fill: #fff"/>
    <path
			d="M9.25,4.4H7.75a.25.25,0,0,0-.25.25v.7a.25.25,0,0,0,.25.25h1.5a.25.25,0,0,0,.25-.25v-.7a.25.25,0,0,0-.25-.25Z"
			style="fill: #fff"/>
  </g>
  <text 
    transform="translate(8 5.4)" 
    style="font-size: 1px;fill: #000;">{{this.day}}</text>

	<line
		:x1="5 - (Math.sin(hAngle) * (hHandLengthRatio * handsTailLength))"
		:y1="5 + (Math.cos(hAngle) * (hHandLengthRatio * handsTailLength))"
		:x2="5 + (Math.sin(hAngle) * ((hHandLengthRatio * 5) - handsPadding))"
		:y2="5 - (Math.cos(hAngle) * ((hHandLengthRatio * 5) - handsPadding))"
		:stroke="hHandColor"
		:stroke-width="thickness * 2.5"
		stroke-linecap="square"
	/>

	<line
		:x1="5 - (Math.sin(mAngle) * (mHandLengthRatio * handsTailLength))"
		:y1="5 + (Math.cos(mAngle) * (mHandLengthRatio * handsTailLength))"
		:x2="5 + (Math.sin(mAngle) * ((mHandLengthRatio * 5) - handsPadding))"
		:y2="5 - (Math.cos(mAngle) * ((mHandLengthRatio * 5) - handsPadding))"
		:stroke="mHandColor"
		:stroke-width="thickness * 2"
		stroke-linecap="square"
	/>

	<line
		:x1="5 - (Math.sin(sAngle) * (sHandLengthRatio * handsTailLength))"
		:y1="5 + (Math.cos(sAngle) * (sHandLengthRatio * handsTailLength))"
		:x2="5 + (Math.sin(sAngle) * ((sHandLengthRatio * 5) - handsPadding))"
		:y2="5 - (Math.cos(sAngle) * ((sHandLengthRatio * 5) - handsPadding))"
		:stroke="sHandColor"
		:stroke-width="thickness / 2"
		stroke-linecap="square"
	/>
	<circle
		:cx="5 + (Math.sin(sAngle) * ((sHandLengthRatio * 5) - handsPadding))"
		:cy="5 - (Math.cos(sAngle) * ((sHandLengthRatio * 5) - handsPadding))"
		r="0.35"
		:fill="sHandColor"
	/>

</svg>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import gsap from "gsap";
import * as tinycolor from 'tinycolor2';

export default defineComponent({
	props: {
		thickness: {
			type: Number,
			default: 0.1
		}
	},

	data() {
		return {
			now: new Date(),
			day: 0,
			enabled: true,

			graduationsPadding: 0.5,
			handsPadding: 1,
			handsTailLength: 0.7,
			hHandLengthRatio: 0.8,
			mHandLengthRatio: 1,
			sHandLengthRatio: 0.8,

			computedStyle: getComputedStyle(document.documentElement)
		};
	},

	computed: {
		dark(): boolean {
			return tinycolor(this.computedStyle.getPropertyValue('--bg')).isDark();
		},

		majorGraduationColor(): string {
			return this.dark ? 'rgba(255, 255, 255, 1)' : 'rgba(0, 0, 0, 1)';
		},
		minorGraduationColor(): string {
			return this.dark ? 'rgba(255, 255, 255, 1)' : 'rgba(0, 0, 0, 1)';
		},

		sHandColor(): string {
			return 'rgba(189, 36, 32, 1)';
		},
		mHandColor(): string {
			return this.dark ? 'rgba(255, 255, 255, 1)' : 'rgba(0, 0, 0, 1)';
		},
		hHandColor(): string {
			return this.dark ? 'rgba(255, 255, 255, 1)' : 'rgba(0, 0, 0, 1)';
		},
		backgroundColor(): string {
			return this.dark ? 'rgba(0, 0, 0, 1)' : 'rgba(255, 255, 255, 1)';
		},

		ms(): number {
			return this.now.getMilliseconds();
		},
		s(): number {
			return this.now.getSeconds();
		},
		m(): number {
			return this.now.getMinutes();
		},
		h(): number {
			return this.now.getHours();
		},

		hAngle(): number {
			return Math.PI * (this.h % 12 + this.m / 60) / 6;
		},
		mAngle(): number {
			return Math.PI * this.m / 30;
		},
		// Swiss
		sAngle(): number {
			const k = Math.PI * (this.s + this.ms / 1000) / 29;
			return (k >= Math.PI * 2) ? Math.PI * 2 : k;
		},

		graduations(): any {
			const angles = [];
			for (let i = 0; i < 60; i++) {
				const angle = Math.PI * i / 30;
				angles.push(angle);
			}

			return angles;
		}
	},

	mounted() {
		const update = () => {
			if (this.enabled) {
				this.tick();
				setTimeout(update, 200);
			}
		};
		update();
	},

	beforeUnmount() {
		this.enabled = false;
	},

	methods: {
		tick() {
			this.now = new Date();
			this.day = this.now.getDate();
		}
	}
});
</script>

<style lang="scss" scoped>
.mbcofsoe {
	display: block;
}
</style>
