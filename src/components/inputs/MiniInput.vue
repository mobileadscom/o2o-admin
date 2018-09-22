<template>
	<div class="input" :style="inputWrapperStyle">
	    <input :type="type" :id="id" :maxlength="maxlength" ref="id" v-bind:value="value" v-on:input="checkVal($event)" :class="{ highlighted: highlighted}" v-on:focus="checkFocus" v-on:blur="checkBlur" :style="inputStyle" :autofocus="focus" />
	    <div class="line" :rel="id" :style="lineStyle"></div>
	    <label :for="id" v-html="label" :style="labelStyle"></label>
	    <div class="error"> {{error}} </div>
	</div>
</template>

<script type="text/javascript">
	export default {
		name: 'MiniInput',
		data () {
			return {
				labelStyle: {
					color: '#aaa'
				},
				lineStyle: {
					backgroundColor: this.color ? this.color : 'blue'
				},
				inputStyle: {
					color: this.fontColor ? this.fontColor : '#444'
				},
				inputWrapperStyle: this.wrapperStyle ? this.wrapperStyle : {},
				blurColor: '#aaa',
				highlightedColor: this.color ? this.color : 'blue',
				highlighted: false,
			};
		},
		methods: {
			correctColors: function() {
				if (this.error) {
					this.labelStyle.color = '#f53c4b';
					this.lineStyle.backgroundColor = '#f53c4b';
				}
				else {
					if (this.$refs.id == document.activeElement) {
						this.labelStyle.color = this.highlightedColor;
						this.lineStyle.backgroundColor = this.highlightedColor;
					}
					else {
						this.labelStyle.color = this.blurColor;
					}
				}
			},
			checkFocus: function() {
				this.highlighted = true;
				this.correctColors();
			},
			checkBlur: function() {
				this.$emit('validate');
				this.correctColors();
			},
			checkVal: function() {
				this.$emit('input', event.target.value);
			},
			checkStatus: function() {
				if (this.value) {
					this.highlighted = true;
					this.correctColors();
				}
				else {
					this.highlighted = false;
					this.correctColors();
				}
			}
		},
		mounted() {
			this.checkStatus();
		},
		updated() {
			this.checkStatus();
		},
		props: ['id', 'value', 'label', 'maxlength', 'color', 'type', 'fontColor', 'focus', 'error', 'wrapperStyle'],
	}
</script>

<style type="text/css" scoped>
.input input {
  border: none;
  border-bottom: 1px solid #aaa;
  width: 100%;
  outline:none;
  background-color: transparent;
  font-size: 16px;
}

.input {
  position: relative;
  padding: 5px;
  width: 90%;
  max-width: 600px;
  margin: 15px 15px 10px;
  font-family: Arial;
}

.input.sm {
  max-width: 300px;
}

.input label{
  font-size: 16px;
  position: absolute;
  top: 6px;
  left: 5px;
  font-weight: 500;
  -webkit-transition: all 0.3s ease-in-out 0s;
  transition: all 0.3s ease-in-out 0s;
  pointer-events: none;
}

.input input:focus ~ label, .input input.highlighted ~ label {
  top: -10px;
  left: 5px;
  font-size: 12px;
  font-weight: 500;
  /*color: #FF59DB;*/
}

.input .line{
  content: "";
  width: 100%;
  height: 1px;
  visibility: hidden;
  -webkit-transform: scaleX(0) translateY(-1px);
  transform: scaleX(0) translateY(-1px);
  -webkit-transition: all 0.3s ease-in-out 0s;
  transition: all 0.3s ease-in-out 0s;
}

.input input:focus ~ .line {
  visibility: visible;
  -webkit-transform: scaleX(1) translateY(-1px);
  transform: scaleX(1) translateY(-1px);
}

.input .error {
  font-size: 12px;
  color: #ff3c4b;
  text-align: left;
  position: absolute;
  left: 5px;
  bottom: -10px;
}
</style>