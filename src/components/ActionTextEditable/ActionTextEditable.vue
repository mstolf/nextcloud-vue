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
All undocumented attributes will be bound to the textarea. e.g. `maxlength`

```
<Actions>
	<ActionTextEditable icon="icon-edit" value="This is a textarea" />
	<ActionTextEditable icon="icon-edit" :disabled="true" value="This is a disabled textarea" />
	<ActionTextEditable icon="icon-edit" title="Please edit the text" value="This is a textarea with title" />
</Actions>
```
</docs>

<template>
	<li class="action" :class="{ 'action--disabled': disabled }">
		<span class="action-text-editable"
			@click="onClick">
			<!-- icon -->
			<span :class="[isIconUrl ? 'action-text-editable__icon--url' : icon]"
				:style="{ backgroundImage: isIconUrl ? `url(${icon})` : null }"
				class="action-text-editable__icon" />

			<!-- form and input -->
			<form ref="form"
				class="action-text-editable__form"
				:disabled="disabled"
				@submit.prevent="onSubmit">
				<input :id="id" type="submit" class="action-text-editable__submit">

				<!-- title -->
				<strong v-if="title" class="action-text__title">
					{{ title }}
				</strong>

				<textarea :disabled="disabled"
					:value="value"
					v-bind="$attrs"
					:class="['action-text-editable__textarea', { focusable: isFocusable }]"
					@input="onInput" />

				<!-- allow the custom font to inject a ::before
					not possible on input[type=submit] -->
				<label v-show="!disabled" :for="id" class="action-text-editable__label">
					<ArrowRight :size="20" title="" decorative />
				</label>
			</form>
		</span>
	</li>
</template>

<script>
import ActionTextMixin from '../../mixins/actionText.js'
import GenRandomId from '../../utils/GenRandomId.js'

import ArrowRight from 'vue-material-design-icons/ArrowRight'

export default {
	name: 'ActionTextEditable',

	components: {
		ArrowRight,
	},

	mixins: [ActionTextMixin],

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
		 * disabled state of the text area
		 */
		disabled: {
			type: Boolean,
			default: false,
		},
		/**
		 * value attribute of the input field
		 */
		value: {
			type: String,
			default: '',
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
		onInput(event) {
			/**
			 * Emitted on input events of the text field
			 *
			 * @type {Event|Date}
			 */
			this.$emit('input', event)
			/**
			 * Emitted when the inputs value changes
			 *
			 * @type {string|Date}
			 */
			this.$emit('update:value', event.target.value)
		},
		onSubmit(event) {
			event.preventDefault()
			event.stopPropagation()
			if (!this.disabled) {
				/**
				 * Emitted on submit of the input field
				 *
				 * @type {Event}
				 */
				this.$emit('submit', event)
			} else {
				// ignore submit
				return false
			}
		},
	},
}
</script>

<style lang="scss" scoped>
@import '../../assets/inputs';
@import '../../assets/action';
@include action-active;
@include action--disabled;

$input-margin: 4px;

.action-text-editable {
	display: flex;
	align-items: flex-start;

	width: 100%;
	height: auto;
	margin: 0;
	padding: 0;

	cursor: pointer;
	white-space: nowrap;

	opacity: $opacity_normal;
	color: var(--color-main-text);
	border: 0;
	border-radius: 0; // otherwise Safari will cut the border-radius area
	background-color: transparent;
	box-shadow: none;

	font-weight: normal;
	line-height: $clickable-area;

	&:hover,
	&:focus {
		opacity: $opacity_full;
	}

	& > span {
		cursor: pointer;
		white-space: nowrap;
	}

	&__icon {
		min-width: 0; /* Overwrite icons*/
		min-height: 0;
		/* Keep padding to define the width to
			assure correct position of a possible text */
		padding: #{math.div($clickable-area, 2)} 0 #{math.div($clickable-area, 2)} $clickable-area;

		background-position: #{$icon-margin} center;
		background-size: $icon-size;
	}

	// Forms & text inputs
	&__form {
		display: flex;
		flex: 1 1 auto;
		flex-direction: column;

		position: relative;
		margin: $input-margin 0;
		padding-right: $icon-margin;
	}

	&__submit {
		position: absolute;
		left: -10000px;
		top: auto;
		width: 1px;
		height: 1px;
		overflow: hidden;
	}

	&__label {
		display: flex;
		align-items: center;
		justify-content: center;

		// bottom-right corner
		position: absolute;
		right: $icon-margin + 1;
		bottom: 1px;
		width: #{$clickable-area - $input-margin * 2};
		height: #{$clickable-area - $input-margin * 2};
		box-sizing: border-box;
		margin: 0;
		padding: 7px 6px;

		opacity: $opacity_full;
		color: var(--color-text-lighter);
		border: 0;
		border-radius: 50%;
		/* Avoid background under border */
		background-color: var(--color-main-background);
		background-clip: padding-box;

		&, * {
			cursor: pointer;
		}
	}

	/* Inputs inside popover supports text, submit & reset */
	&__textarea {
		flex: 1 1 auto;

		min-height: #{$clickable-area * 2 - $input-margin * 2}; /* twice the element margin-y */
		max-height: #{$clickable-area * 3 - $input-margin * 2}; /* twice the element margin-y */
		// block width widening
		min-width: $clickable-area * 4;
		width: 100% !important;
		margin: 0;

		// if disabled, change cursor
		&:disabled {
			cursor: default;
		}

		/* only show confirm borders if input is not focused */
		&:not(:active):not(:hover):not(:focus) {
			&:invalid {
				& + .action-text-editable__label {
					background-color: var(--color-error);
				}
			}
			&:not(:disabled) + .action-text-editable__label {
				&:active,
				&:hover,
				&:focus {
					background-color: var(--color-primary-element);
					color: var(--color-primary-text);
				}
			}
		}
		&:active,
		&:hover,
		&:focus {
			&:not(:disabled) + .action-text-editable__label {
				/* above previous input */
				z-index: 2;

				border-color: var(--color-primary-element);
				border-left-color: transparent;
			}
		}
	}
}

// if a form is the last of the list
// add the same bottomMargin as the right padding
// for visual balance
li:last-child > .action-text-editable {
	margin-bottom: $icon-margin - $input-margin;
}

// same for first item
li:first-child > .action-text-editable {
	margin-top: $icon-margin - $input-margin;
}

</style>
