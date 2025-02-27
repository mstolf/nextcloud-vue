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
-->

<docs>
This is a simple progress bar component.
## Usage:

### Small
```vue
<ProgressBar :value="60" />
```

### Medium
```vue
<ProgressBar :value="60" size="medium" />
```

### error
```vue
<ProgressBar :value="60" :error="true" />
```

</docs>

<template>
	<progress class="progress-bar vue"
		:class="{ 'progress-bar--error': error }"
		:style="{'--progress-bar-height': height }"
		:value="value"
		max="100" />
</template>

<script>
export default {

	name: 'ProgressBar',

	props: {
		/**
		 * An integer between 1 and 100
		 */
		value: {
			type: Number,
			default: 0,
			validator(value) {
				return value >= 0
					&& value <= 100
			},
		},
		/**
		 * Determines the height of the progressbar.
		 * Possible values:
		 * - 'small' (default)
		 * - 'medium'
		 */
		size: {
			type: String,
			default: 'small',
			validator(value) {
				return ['small', 'medium'].indexOf(value) !== -1
			},
		},
		/**
		 * Applies an error color to the progressbar if true.
		 */
		error: {
			type: Boolean,
			default: false,
		},
	},
	computed: {
		height() {
			if (this.size === 'small') {
				return '4px'
			}
			return '6px'
		},
	},
}

</script>

<style lang="scss" scoped>

.progress-bar {
	display: block;
	width: 100%;
	background: var(--color-background-dark);
	border: 0;
	padding: 0;
	height: var(--progress-bar-height);
	border-radius: calc(var(--progress-bar-height) / 2);
	&::-webkit-progress-bar {
		height: var(--progress-bar-height);
	}
	&::-webkit-progress-value {
		background: linear-gradient(40deg, var(--color-primary-element) 0%, var(--color-primary-element-light) 100%);
		border-radius: calc(var(--progress-bar-height) / 2);
	}
	&::-moz-progress-bar {
		background: linear-gradient(40deg, var(--color-primary-element) 0%, var(--color-primary-element-light) 100%);
		border-radius: calc(var(--progress-bar-height) / 2);
	}
	&--error {
		// Override previous values
		&::-moz-progress-bar {
			background: var(--color-error) !important;
		}
		&::-webkit-progress-value {
			background: var(--color-error) !important;
		}
	}
}

</style>
