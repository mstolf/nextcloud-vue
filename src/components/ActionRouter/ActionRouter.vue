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

<template>
	<li class="action">
		<router-link :to="to"
			:exact="exact"
			class="action-router focusable"
			:aria-label="ariaLabel"
			rel="nofollow noreferrer noopener"
			@click.native="onClick">
			<!-- @slot Manually provide icon -->
			<slot name="icon">
				<span :class="[isIconUrl ? 'action-router__icon--url' : icon]"
					:style="{ backgroundImage: isIconUrl ? `url(${icon})` : null }"
					class="action-router__icon" />
			</slot>

			<!-- long text with title -->
			<p v-if="title">
				<strong class="action-router__title">
					{{ title }}
				</strong>
				<br>
				<!-- white space is shown on longtext, so we can't
				put {{ text }} on a new line for code readability -->
				<span class="action-router__longtext" v-text="text" />
			</p>

			<!-- long text only -->
			<!-- white space is shown on longtext, so we can't
			put {{ text }} on a new line for code readability -->
			<p v-else-if="isLongText"
				class="action-router__longtext"
				v-text="text" />

			<!-- default text display -->
			<span v-else class="action-router__text">{{ text }}</span>

			<!-- fake slot to gather inner text -->
			<slot v-if="false" />
		</router-link>
	</li>
</template>

<script>
import ActionTextMixin from '../../mixins/actionText.js'

export default {
	name: 'ActionRouter',

	mixins: [ActionTextMixin],

	props: {
		/**
		 * router-link to prop [https://router.vuejs.org/api/#to](https://router.vuejs.org/api/#to)
		 */
		to: {
			type: [String, Object],
			default: '',
			required: true,
		},
		/**
		 * router-link exact prop [https://router.vuejs.org/api/#exact](https://router.vuejs.org/api/#exact)
		 */
		exact: {
			type: Boolean,
			default: false,
		},
	},
}
</script>

<style lang="scss" scoped>
@import '../../assets/action';
@include action-active;
@include action-item('router');
@include action--disabled;

</style>
