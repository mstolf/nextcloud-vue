<!--
  - @copyright Copyright (c) 2019 John Molakvoæ <skjnldsv@protonmail.com>
  -
  - @author John Molakvoæ <skjnldsv@protonmail.com>
  - @author Marco Ambrosini <marcoambrosini@pm.me>
  -
  - @license GNU AGPL version 3 or any later version
  -
  - This program is free software: you can redistribute it and/or modify
  - it under the terms of the GNU Affero General Public License as
  - published by the Free Software Foundation, either version 3 of the
  - License, or (at your option) any later version.
  -
  - This program is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program. If not, see <http://www.gnu.org/licenses/>.
  -
  -->

<docs>
This component is made to be used inside of the [Actions](#Actions) component slots.

```vue
	<Actions>
		<ActionCheckbox @change="alert('(un)checked !')">First choice</ActionCheckbox>
		<ActionCheckbox value="second" @change="alert('(un)checked !')">Second choice</ActionCheckbox>
		<ActionCheckbox :checked="true" @change="alert('(un)checked !')">Third choice (checked)</ActionCheckbox>
		<ActionCheckbox :disabled="true" @change="alert('(un)checked !')">Second choice (disabled)</ActionCheckbox>
	</Actions>
```
</docs>

<template>
	<li class="action" :class="{ 'action--disabled': disabled }">
		<span class="action-checkbox">
			<input :id="id"
				ref="checkbox"
				:disabled="disabled"
				:checked="checked"
				:value="value"
				:class="{ focusable: isFocusable }"
				type="checkbox"
				class="checkbox action-checkbox__checkbox"
				@keydown.enter.exact.prevent="checkInput"
				@change="onChange">
			<label ref="label" :for="id" class="action-checkbox__label">{{ text }}</label>

			<!-- fake slot to gather inner text -->
			<slot v-if="false" />
		</span>
	</li>
</template>

<script>
import ActionGlobalMixin from '../../mixins/actionGlobal.js'
import GenRandomId from '../../utils/GenRandomId.js'

export default {
	name: 'ActionCheckbox',

	mixins: [ActionGlobalMixin],

	props: {
		/**
		 * id attribute of the checkbox element
		 */
		id: {
			type: String,
			default: () => 'action-' + GenRandomId(),
			validator: id => id.trim() !== '',
		},

		/**
		 * checked state of the the checkbox element
		 */
		checked: {
			type: Boolean,
			default: false,
		},

		/**
		 * value of the checkbox input
		 */
		value: {
			type: [String, Number],
			default: '',
		},

		/**
		 * disabled state of the checkbox element
		 */
		disabled: {
			type: Boolean,
			default: false,
		},
	},

	computed: {
		/**
		 * determines if the action is focusable
		 *
		 * @return {boolean} is the action focusable ?
		 */
		isFocusable() {
			return !this.disabled
		},
	},

	methods: {
		checkInput(event) {
			// by clicking we also trigger the change event
			this.$refs.label.click()
		},
		onChange(event) {
			/**
			 * Emitted when the checkbox state is changed
			 *
			 * @type {boolean}
			 */
			this.$emit('update:checked', this.$refs.checkbox.checked)

			/**
			 * Emitted when the checkbox state is changed
			 *
			 * @type {Event}
			 */
			this.$emit('change', event)

			if (this.$refs.checkbox.checked) {
				/**
				 * Emitted when the checkbox is checked
				 *
				 * @type {Event}
				 */
				this.$emit('check')
			} else {
				/**
				 * Emitted when the checkbox is unchecked
				 *
				 * @type {Event}
				 */
				this.$emit('uncheck')
			}
		},
	},
}
</script>

<style lang="scss" scoped>
@import '../../assets/action';
@include action-active;
@include action--disabled;

.action-checkbox {
	display: flex;
	align-items: flex-start;

	width: 100%;
	height: auto;
	margin: 0;
	padding: 0;

	cursor: pointer;
	white-space: nowrap;

	color: var(--color-main-text);
	border: 0;
	border-radius: 0; // otherwise Safari will cut the border-radius area
	background-color: transparent;
	box-shadow: none;

	font-weight: normal;
	line-height: $clickable-area;

	/* checkbox/radio fixes */
	&__checkbox {
		position: absolute;
		top: auto;
		left: -10000px;

		overflow: hidden;

		width: 1px;
		height: 1px;
		&:focus + .action-checkbox__label {
			opacity: $opacity_full;
		}
	}

	&__label {
		display: flex;
		align-items: center; // align checkbox to text

		width: 100%;
		padding: 0 !important;
		padding-right: $icon-margin !important;

		opacity: $opacity_normal;
		// checkbox-width is 12px, border is 2
		// (44 - 14 - 2) / 2 = 14
		&::before {
			margin: 0 14px 0 !important;
		}
	}

	&--disabled {
		&,
		.action-checkbox__label {
			cursor: pointer;
		}
	}

	&:not(.action-checkbox--disabled):hover,
	&:not(.action-checkbox--disabled):focus {
		.action-checkbox__label {
			opacity: $opacity_full;
		}
	}
}

</style>
