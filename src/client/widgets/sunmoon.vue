<template>
<div class="mkw-sunmoon" :class="{ _panel: !props.transparent }">
	<div class="lunar-calendar">
		<p class="moonface"><img :src="moonFace"></p>
		<p class="moonage">{{ moonAge }}</p>
	</div>
	<div class="suntime">
		<div>
			<p><i class="fas fa-arrow-up"></i>{{ $ts.sunRise }}: <b>{{ sunRiseTime }}</b></p>
		</div>
		<div>
			<p><i class="fas fa-arrow-down"></i>{{ $ts.sunSet }}: <b>{{ sunSetTime }}</b></p>
		</div>
	</div>
</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import SunCalc from "suncalc";
import define from './define';
import * as os from '@client/os';
import { twemojiSvgBase } from '@client/../misc/twemoji-base';

const widget = define({
	name: 'sunmoon',
	props: () => ({
		transparent: {
			type: 'boolean',
			default: false,
		},
		latitude: {
			type: 'number',
			default: 35,
		},
		longitude: {
			type: 'number',
			default: 135,
		},
	})
});

export default defineComponent({
	extends: widget,
	data() {
		return {
			now: new Date(),
			year: 2000,
			month: 1,
			day: 1,
			sunRiseTime: new Date(),
			sunSetTime: new Date(),
			moonAge: 0.0,
			moonFace: `${twemojiSvgBase}/1f313.svg`,
			clock: 0
		};
	},
	created() {
		this.tick();
		this.clock = setInterval(this.tick, 8000);
	},
	beforeUnmount() {
		clearInterval(this.clock);
	},
	methods: {
		tick() {
			const now = new Date();
			const nd = now.getDate();
			const nm = now.getMonth();
			const ny = now.getFullYear();

			this.year = ny;
			this.month = nm + 1;
			this.day = nd;

			const hour12 = now;
			hour12.setHours(12);
			hour12.setMinutes(0);
			hour12.setSeconds(0);

			const sunTimes = SunCalc.getTimes(hour12, this.props.latitude, this.props.longitude);
			const MoonTimes = SunCalc.getMoonIllumination(hour12);

			this.sunRiseTime = sunTimes["sunrise"];
			this.sunSetTime = sunTimes["sunset"];
			const moonPhase = MoonTimes["phase"];
			this.moonAge = 29.5 * moonPhase;
			const moonFaceImg = ["a", "2", "3", "4", "d", "6", "7", "8"][Math.floor(8 * this.moonAge)];
			this.moonFace = `${twemojiSvgBase}/1f31${moonFaceImg}.svg`;
		}
	}
});
</script>

<style lang="scss" scoped>
.mkw-sunmoon {
	padding: 16px 0;

	&:after {
		content: "";
		display: block;
		clear: both;
	}

	> .lunar-calendar {
		float: left;
		width: 50%;
		text-align: center;

		> p {
			margin: 0;
			line-height: 18px;
			font-size: 0.9em;
		}

		> .moonface {
			margin: 10px 0;
			line-height: 72px;
			font-size: 4em;
		}
	}

	> .suntime {
		display: block;
		float: left;
		width: 40%;
		padding: 0 16px 0 0;
		box-sizing: border-box;

		> div {
			margin-bottom: 8px;

			&:last-child {
				margin-bottom: 4px;
			}

			> p {
				margin: 0 0 2px 0;
				font-size: 1em;
				line-height: 18px;

			// sunrise sunset time
				> b {
					margin-left: 2px;
				}
			}
		}
	}
}
</style>
