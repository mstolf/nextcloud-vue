<!--
 - @copyright Copyright (c) 2020 Marco Ambrosini <marcoambrosini@pm.me>
 -
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
 - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 - GNU Affero General Public License for more details.
 -
 - You should have received a copy of the GNU Affero General Public License
 - along with this program. If not, see <http://www.gnu.org/licenses/>.
 -
 -->

<docs>

### General description
General purpose button component. See props for different options.
[Use material design icons only for icons](https://www.npmjs.com/package/vue-material-design-icons) and remember to set their size to 20.

### Usage
### Custom icon slot
To be used with `vue-material-design-icons` only. For icon classes use the `default-icon` slot.
It can be used with one or multiple actions.
```
<template>
<div class="wrapper">
	<!-- Style selector -->
	<div class="grid">
		<CheckboxRadioSwitch :checked.sync="style" value="text" name="style" type="radio">Text only</CheckboxRadioSwitch>
		<CheckboxRadioSwitch :checked.sync="style" value="icon" name="style" type="radio">Icon only</CheckboxRadioSwitch>
		<CheckboxRadioSwitch :checked.sync="style" value="icontext" name="style" type="radio">Icon and text</CheckboxRadioSwitch>
		<CheckboxRadioSwitch :checked.sync="disabled" type="checkbox">Disabled</CheckboxRadioSwitch>
		<!--<CheckboxRadioSwitch :checked.sync="readonly" type="checkbox">Read-only</CheckboxRadioSwitch>-->
	</div>

	<h5>Standard buttons</h5>
	<div class="grid">
		<p>Tertiary, no background</p>
		<p>Tertiary</p>
		<p>Secondary</p>
		<p>Primary</p>
		<Button
			:disabled="disabled"
			:readonly="readonly"
			type="tertiary-no-background">
			<template v-if="style.indexOf('icon') !== -1" #icon>
				<Video
					:size="20" />
			</template>
			<template v-if="style.indexOf('text') !== -1">Example text</template>
		</Button>
		<Button
			:disabled="disabled"
			:readonly="readonly"
			type="tertiary">
			<template v-if="style.indexOf('icon') !== -1" #icon>
				<Video
					:size="20" />
			</template>
			<template v-if="style.indexOf('text') !== -1">Example text</template>
		</Button>
		<Button
			:disabled="disabled"
			:readonly="readonly">
			<template v-if="style.indexOf('icon') !== -1" #icon>
				<Video
					title=""
					:size="20" />
			</template>
			<template v-if="style.indexOf('text') !== -1">Example text</template>
		</Button>
		<Button
			:disabled="disabled"
			:readonly="readonly"
			type="primary">
			<template v-if="style.indexOf('icon') !== -1" #icon>
				<Video
					:size="20" />
			</template>
			<template v-if="style.indexOf('text') !== -1">Example text</template>
		</Button>
	</div>

	<!-- Wide button -->
	<h5>Wide button</h5>
	<Button
		:disabled="disabled"
		:readonly="readonly"
		:wide="true"
		text="Example text">
		<template #icon>
			<Video
				title=""
				:size="20" />
		</template>
		Example text
	</Button>

	<!-- Special buttons -->
	<h5>Special buttons</h5>
	<div class="grid">
		<p>Success</p>
		<p>Warning</p>
		<p>Error</p>
		<p> - </p>
		<Button
			:disabled="disabled"
			:readonly="readonly"
			type="success">
			<template #icon>
				<Video
					:size="20" />
			</template>
			Example text
		</Button>
		<Button
			:disabled="disabled"
			:readonly="readonly"
			type="warning">
			<template #icon>
				<Video
					title=""
					:size="20" />
			</template>
			Example text
		</Button>
		<Button
			:disabled="disabled"
			:readonly="readonly"
			type="error">
			<template #icon>
				<Video
					:size="20" />
			</template>
			Example text
		</Button>
		<p> - </p>
	</div>
</div>

</template>
<script>
import Video from 'vue-material-design-icons/Video'

export default {
	components: {
		Video,
	},
	data() {
		return {
			toggled: false,
			disabled: false,
			readonly: false,
			style: 'icontext',
		}
	}
}
</script>

<style lang="scss" scoped>
.wrapper {
	padding: 0 12px;
}

.grid {
	display: grid;
	grid-template-columns: 1fr 1fr 1fr 1fr;
	grid-template-rows: repeat(auto-fill, auto);
	position: relative;
	margin: 12px 0;
}

h5 {
	font-weight: bold;
	margin: 40px 0 20px 0;
}

p {
	text-align: center;
	margin: 4px 0 12px 0;
	color: var(--color-text-lighter)
}

button {
	margin: auto;
}
</style>
```
</docs>

<template>
	<button class="button-vue"
		v-bind="$attrs"
		:class="buttonClassObject"
		:aria-label="ariaLabel"
		:type="nativeType"
		:disabled="disabled"
		v-on="$listeners"
		@keydown.enter="makeActive"
		@keyup.enter="makeInactive"
		@click="handleClick"
		@blur="handleBlur"
		@keyup.tab.exact="handleTabUp"
		@keyup.shift.tab="handleTabUp">
		<span class="button-vue__wrapper">
			<span v-if="hasIcon" class="button-vue__icon">
				<!-- @slot The material design icon slot -->
				<slot name="icon" />
			</span>
			<span v-if="hasText" class="button-vue__text">
				<slot />
			</span>
		</span>
	</button>
</template>
<script>

export default {
	name: 'Button',

	props: {
		/**
		 * Toggles the disabled state of the button on and off.
		 */
		disabled: {
			type: Boolean,
			default: false,
		},

		/**
		 * Specifies the button type
		 * Accepted values: primary, secondary, tertiary, error, warning, success. If left empty,
		 * the default button style will be applied.
		 */
		type: {
			type: String,
			validator(value) {
				return ['primary', 'secondary', 'tertiary', 'tertiary-no-background', 'error', 'warning', 'success'].indexOf(value) !== -1
			},
			default: 'secondary',
		},

		/**
		 * Specifies the button native type
		 * Accepted values: submit, reset, button. If left empty,
		 * the default "button" type will be used.
		 */
		nativeType: {
			type: String,
			validator(value) {
				return ['submit', 'reset', 'button'].indexOf(value) !== -1
			},
			default: 'button',
		},

		/**
		 * Specifies whether the button should span all the available width.
		 * By default, buttons span the whole width of the container.
		 */
		wide: {
			type: Boolean,
			default: false,
		},

		/**
		 * Always try to provide an aria-label to your button. Make it more
		 * specific than the button's title by provide some more context. E.g. if
		 * the title of the button is "send" in the Mail app, the aria label could
		 * be "Send email".
		 */
		ariaLabel: {
			type: String,
			default: null,
		},
	},

	data() {
		return {
			/**
			 * Keeps track of whether the element's focus status is due to having
			 * tabbed to it. We use this to display a thick 'focus outline' only
			 * when the user is navigating with the keyboard.
			 */
			tabbed: false,

			/**
			 * Making sure the slots are reactive
			 */
			slots: this.$slots,
		}
	},

	computed: {
		hasText() {
			return this.slots?.default !== undefined
				&& this.slots?.default[0]?.text
		},

		hasIcon() {
			return this.slots.icon !== undefined
		},

		iconOnly() {
			return this.hasIcon && !this.hasText
		},

		textOnly() {
			return !this.hasIcon && this.hasText
		},

		iconAndText() {
			return this.hasIcon && this.hasText
		},

		text() {
			return this.hasText ? this.slots.default[0].text.trim() : null
		},

		// Classes applied to the button element
		buttonClassObject() {
			return {
				// If icon only, some additional css rules are required
				'button-vue--icon-only': this.iconOnly,
				'button-vue--text-only': this.textOnly,
				'button-vue--icon-and-text': this.iconAndText,
				[`button-vue--vue-${this.type}`]: this.type,
				'button-vue--wide': this.wide,
				'button-vue--tabbed': this.tabbed,
			}
		},
	},

	beforeUpdate() {
		// $slots is not reactive, this make sure we are able to detect changes
		this.slots = this.$slots
	},

	mounted() {
		/**
		 * Always fill either the text prop or the ariaLabel one.
		 */
		if (!this.text && !this.ariaLabel) {
			console.warn('You need to fill either the text or the ariaLabel props in the button component.', {
				text: this.text,
				ariaLabel: this.ariaLabel,
			},
			this)
		}
	},

	methods: {
		/**
		 * Removes the tabbed state of the button.
		 */
		handleClick() {
			this.tabbed = false
		},

		/**
		 * When the tab key is lifted, the button has been "tabbed in",
		 * see comments on the `tabbed` variable declared in the data.
		 */
		handleTabUp() {
			this.tabbed = true
		},

		/**
		 * Everytime the button is blurred, we remove the tabbed state.
		 */
		handleBlur() {
			this.tabbed = false
		},

		/**
		 * When the button is reached via keyboard navigation and pressed using
		 * the enter key, we slightly change the styles to provide an "active-like"
		 * feedback. When using the mouse this is achieved with the ripple effect.
		 */
		makeActive() {
			this.tabbed = false
		},

		makeInactive() {
			this.tabbed = true
		},
	},
}

</script>

<style lang="scss" scoped>

.button-vue {
	position: relative;
	width: fit-content;
	overflow: hidden;
	border: 0;
	padding: 0;
	font-size: var(--default-font-size);
	font-weight: bold;
	min-height: $clickable-area;
	min-width: $clickable-area;
	display: flex;
	align-items: center;
	justify-content: center;

	// Cursor pointer on element and all children
	cursor: pointer;
	& * {
		cursor: pointer;
	}
	border-radius: math.div($clickable-area, 2);
	transition: background-color 0.1s linear !important;
	transition: border 0.1s linear;

	// No outline feedback for focus. Handled with a toggled class in js (see data)
	&:focus {
		outline: none;
	}

	&:disabled {
		cursor: default;
		& * {
			cursor: default;
		}
		opacity: $opacity_disabled;
		// Gives a wash out effect
		filter: saturate($opacity_normal);
	}

	// Default button type
	background-color: var(--color-primary-element-lighter);
	color: var(--color-primary-light-text);
	&:hover:not(:disabled) {
		background-color: var(--color-primary-light-hover);
	}

	// Back to the default color for this button when active
	// TODO: add ripple effect
	&:active {
		background-color: var(--color-primary-element-lighter);
	}

	&__wrapper {
		display: inline-flex;
		align-items: center;
		justify-content: space-around;
	}

	&__icon {
		height: $clickable-area;
		width: $clickable-area;
		min-height: $clickable-area;
		min-width: $clickable-area;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	&__text {
		font-weight: bold;
		margin-bottom: 1px;
		padding: 2px 0;
	}

	// Icon-only button
	&--icon-only {
		width: $clickable-area !important;
	}

	// Text-only button
	&--text-only {
		padding: 0 12px;
		& .button-vue__text {
			margin-left: 4px;
			margin-right: 4px;
		}
	}

	// Icon and text button
	&--icon-and-text {
		padding: 0 16px 0 4px;
	}

	// Wide button spans the whole width of the container
	&--wide {
		width: 100%;
	}

	// We use box-shadow around our buttons instead of an outline, so that the added "border"
	// coincides with the border of the element. It's not possible to add a border-radius to
	// the outline
	&--tabbed, &:focus-visible {
		box-shadow: 0 0 0 2px var(--color-main-text);
		background-color: var(--color-primary-light-hover);
		&.button-vue--vue-primary {
			background-color: var(--color-primary-hover);
		}
		&.button-vue--vue-secondary {
			box-shadow: 0 0 0 2px var(--color-main-text);
		}
		&.button-vue--vue-tertiary-no-background {
			opacity: 1;
		}
		&.button-vue--vue-success {
			background-color: var(--color-success-hover);
		}
		&.button-vue--vue-warning {
			background-color: var(--color-warning-hover);
		}
		&.button-vue--vue-error {
			background-color: var(--color-error-hover);
		}
	}

	// Button types

	// Primary
	&--vue-primary {
		background-color: var(--color-primary-element);
		color: var(--color-primary-text);
		&:hover:not(:disabled) {
			background-color: var(--color-primary-element-hover);
		}
		// Back to the default color for this button when active
		// TODO: add ripple effect
		&:active {
			background-color: var(--color-primary-element);
		}
	}

	// Secondary
	&--vue-secondary {
		color: var(--color-main-text);
		background-color: var(--color-background-dark);
		box-shadow: 0 0 0 2px var(--color-border-dark);
		&:hover:not(:disabled) {
			color: var(--color-main-text);
			background-color: var(--color-background-dark);
			box-shadow: 0 0 0 2px var(--color-primary-element);
		}
	}

	// Tertiary
	&--vue-tertiary {
		color: var(--color-main-text);
		background-color: transparent;
		&:hover:not(:disabled) {
			background-color: var(--color);
			background-color: var(--color-background-hover);
		}
	}

	// Tertiary, no background
	&--vue-tertiary-no-background {
		color: var(--color-main-text);
		background-color: transparent;
		opacity: .7;
		&:hover:not(:disabled) {
			background-color: transparent;
			opacity: 1;
		}
	}

	// Success
	&--vue-success {
		background-color: var(--color-success);
		color: white;
		&:hover:not(:disabled) {
			background-color: var(--color-success-hover);
		}
		// Back to the default color for this button when active
		// : add ripple effect
		&:active {
			background-color: var(--color-success);
		}
	}

	// Warning
	&--vue-warning {
		background-color: var(--color-warning);
		color: white;
		&:hover:not(:disabled) {
			background-color: var(--color-warning-hover);
		}
		// Back to the default color for this button when active
		// TODO: add ripple effect
		&:active {
			background-color: var(--color-warning);
		}
	}

	// Error
	&--vue-error {
		background-color: var(--color-error);
		color: white;
		&:hover:not(:disabled) {
			background-color: var(--color-error-hover);
		}
		// Back to the default color for this button when active
		// TODO: add ripple effect
		&:active {
			background-color: var(--color-error);
		}
	}
}

</style>
