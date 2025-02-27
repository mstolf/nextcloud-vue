<!--
 - @copyright Copyright (c) 2020 Julien Veyssier <eneiluj@posteo.net>
 -
 - @author Julien Veyssier <eneiluj@posteo.net>
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

### General description

This component is meant to be used inside a DashboardWidget component.

</docs>

<template>
	<div @mouseover="hovered = true" @mouseleave="hovered = false">
		<component :is="targetUrl ? 'a' : 'div'"
			:href="targetUrl"
			:target="targetUrl ? '_blank' : undefined"
			:class="{ 'item-list__entry': true, 'item-list__entry--has-actions-menu': gotMenu }"
			@click="onLinkClick">
			<!-- @slot Slot for passing a user avatar. -->
			<slot name="avatar" :avatar-url="avatarUrl" :avatar-username="avatarUsername">
				<Avatar class="item-avatar"
					:size="44"
					:url="avatarUrl"
					:user="avatarUsername"
					:is-no-user="avatarIsNoUser"
					:show-user-status="!gotOverlayIcon" />
			</slot>
			<img v-if="overlayIconUrl"
				class="item-icon"
				alt=""
				:src="overlayIconUrl">
			<div class="item__details">
				<h3 :title="mainText">
					{{ mainText }}
				</h3>
				<p class="message" :title="subText">
					{{ subText }}
				</p>
			</div>
			<Actions v-if="gotMenu" :force-menu="forceMenu" menu-align="right">
				<!-- @slot This slot can be used to provide actions for each dashboard widget item. -->
				<slot name="actions">
					<ActionButton v-for="(m, menuItemId) in itemMenu"
						:key="menuItemId"
						:icon="m.icon"
						:close-after-click="true"
						@click.prevent.stop="$emit(menuItemId, item)">
						{{ m.text }}
					</ActionButton>
				</slot>
			</Actions>
		</component>
	</div>
</template>

<script>
import Avatar from '../Avatar/index.js'
import Actions from '../Actions/index.js'
import ActionButton from '../ActionButton/index.js'

export default {
	name: 'DashboardWidgetItem',
	components: {
		Avatar,
		Actions,
		ActionButton,
	},

	props: {
		/**
		 * The item id (optional)
		 */
		id: {
			type: [String, Number],
			default: undefined,
		},
		/**
		 * The item element is a link to this URL (optional)
		 */
		targetUrl: {
			type: String,
			default: undefined,
		},
		/**
		 * Where to get the avatar image. (optional) Used if avatarUsername is not defined.
		 */
		avatarUrl: {
			type: String,
			default: undefined,
		},
		/**
		 * Name to provide to the Avatar. (optional) Used if avatarUrl is not defined.
		 */
		avatarUsername: {
			type: String,
			default: undefined,
		},
		/**
		 * Is the avatarUsername not a user's name? (optional, false by default)
		 */
		avatarIsNoUser: {
			type: Boolean,
			default: false,
		},
		/**
		 * Small icon to display on the bottom-right corner of the avatar (optional)
		 */
		overlayIconUrl: {
			type: String,
			default: undefined,
		},
		/**
		 * Item main text (mandatory)
		 */
		mainText: {
			type: String,
			required: true,
		},
		/**
		 * Item subline text (optional)
		 */
		subText: {
			type: String,
			default: '',
		},
		/**
		 * An object containing context menu entries that will be displayed for each items (optional)
		 */
		itemMenu: {
			type: Object,
			default: () => { return {} },
		},

		/**
		 * Specify whether the 3 dot menu is forced when only one action is present
		 */
		forceMenu: {
			type: Boolean,
			default: true,
		},
	},

	data() {
		return {
			hovered: false,
		}
	},

	computed: {
		item() {
			return {
				id: this.id,
				targetUrl: this.targetUrl,
				avatarUrl: this.avatarUrl,
				avatarUsername: this.avatarUsername,
				overlayIconUrl: this.overlayIconUrl,
				mainText: this.mainText,
				subText: this.subText,
			}
		},
		gotMenu() {
			return Object.keys(this.itemMenu).length !== 0 || !!this.$slots.actions
		},
		gotOverlayIcon() {
			return this.overlayIconUrl && this.overlayIconUrl !== ''
		},
	},

	methods: {
		onLinkClick(event) {
			if (event.target.closest('.action-item')) {
				event.preventDefault()
			}
		},
	},
}
</script>

<style scoped lang="scss">
.item-list__entry {
	display: flex;
	align-items: flex-start;
	position: relative;
	padding: 8px;

	// Account for action menu
	&--has-actions-menu {
		padding-right: 0 !important;
	}

	&:hover,
	&:focus {
		background-color: var(--color-background-hover);
		border-radius: var(--border-radius-large);
	}
	.item-avatar {
		position: relative;
		margin-top: auto;
		margin-bottom: auto;
	}
	.item__details {
		padding-left: 8px;
		max-height: 44px;
		flex-grow: 1;
		overflow: hidden;
		display: flex;
		flex-direction: column;

		h3,
		.message {
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
		}
		.message span {
			width: 10px;
			display: inline-block;
			margin-bottom: -3px;
		}
		h3 {
			font-size: 100%;
			margin: 0;
		}
		.message {
			width: 100%;
			color: var(--color-text-maxcontrast);
		}
	}

	.item-icon {
		position: relative;
		width: 14px;
		height: 14px;
		margin: 27px -3px 0px -7px;
	}

	button.primary {
		padding: 21px;
		margin: 0;
	}
}
/*
.content-popover {
	height: 0px;
	width: 0px;
	margin-left: auto;
	margin-right: auto;
}
.popover-container {
	width: 100%;
	height: 0px;
}
*/
</style>
