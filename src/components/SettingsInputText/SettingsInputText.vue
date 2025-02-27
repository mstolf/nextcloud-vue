<!--
  -
  - @author Julius Härtl <jus@bitgrid.net>
  - @author Greta Doci <gretadoci@gmail.com>
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
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program. If not, see <http://www.gnu.org/licenses/>.
  -
  -->

<template>
	<form ref="form"
		:disabled="disabled"
		@submit.prevent.stop="onSubmit">
		<div class="input-wrapper">
			<label :for="id" class="action-input__label">{{ label }}</label>
			<input :id="id"
				type="text"
				:value="value"
				:disabled="disabled"
				@input="onInput"
				@change="onChange">
			<input :id="idSubmit"
				:value="submitTranslated"
				type="submit"
				class="action-input__submit">
			<label v-show="!disabled" :for="idSubmit" class="action-input__label" />
			<p v-if="hint" class="hint">
				{{ hint }}
			</p>
		</div>
	</form>
</template>

<script>
import { t } from '../../l10n.js'
import GenRandomId from '../../utils/GenRandomId.js'

export default {
	name: 'SettingsInputText',
	props: {
		/**
		 * label of the select group element
		 */
		label: {
			type: String,
			required: true,
		},

		/**
		 * hint of the select group input
		 */
		hint: {
			type: String,
			default: '',
		},

		/**
		 * value of the select group input
		 */
		value: {
			type: String,
			default: '',
		},

		/**
		 * disabled state of the settings select group input
		 */
		disabled: {
			type: Boolean,
			default: false,
		},

		/**
		 * id attribute of the select group element
		 */
		id: {
			type: String,
			default: () => 'settings-input-text-' + GenRandomId(),
			validator: id => id.trim() !== '',
		},
	},

	data() {
		return {
			submitTranslated: t('Submit'),
		}
	},

	computed: {
		/**
		 * @return {string}
		 */
		idSubmit() {
			return this.id + '-submit'
		},
	},
	methods: {
		onInput(event) {
			this.$emit('input', event)
			/**
			 * Emitted when the inputs value changes
			 *
			 * @type {string}
			 */
			this.$emit('update:value', event.target.value)
		},
		onSubmit(event) {
			if (!this.disabled) {
				/**
				 * Emitted on submit of the input field
				 *
				 * @type {Event}
				 */
				this.$emit('submit', event)
			}
		},
		onChange(event) {
			/**
			 * Emitted on change of the input field
			 *
			 * @type {Event}
			 */
			this.$emit('change', event)
		},
	},
}
</script>

<style lang="scss" scoped>

	$input-margin: 4px;

	.input-wrapper {
		display: flex;
		align-items: flex-start;
		flex-wrap: wrap;
		width: 100%;
		max-width: 400px;
		&__form {
			display: flex;
			align-items: center;
			flex: 1 1 auto;

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

			width: #{$clickable-area - $input-margin * 2};
			height: #{$clickable-area - $input-margin * 2};

			cursor: pointer;

			opacity: $opacity_full;
			color: var(--color-text-lighter);
			border: 1px solid var(--color-border-dark);
			border-left-color: transparent;
			border-radius: 0 var(--border-radius) var(--border-radius) 0;
			/* Avoid background under border */
			background-color: var(--color-main-background);
			background-clip: padding-box;

			font-size: $icon-size;
		}

		&__input[type=text] {
			flex-grow: 1;
			white-space: nowrap;
			text-overflow: ellipsis;
			overflow: hidden;
			cursor: text;
		}

		// if disabled, change cursor
		&:disabled {
			cursor: default;
		}

		.hint {
			color: var(--color-text-lighter);
		}
	}

</style>
