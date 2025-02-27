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

## Usage

### Simplest example with custom item
```vue
<template>
	<DashboardWidget :items="items">
		<template #default="{ item }">
			{{ item.title }}
		</template>
	</DashboardWidget>
</template>

<script>
const myItems = [
	{
		title: 'first',
		content: 'blabla',
	},
	{
		title: 'second',
		content: 'fuzzfuzz',
	},
]
export default {
	name: 'MyDashboardWidget',
	props: [],
	data() {
		return {
			items: myItems
		}
	},
}
</script>
```

### Complete example using DashboardWidgetItem

```vue
<template>
	<DashboardWidget :items="items"
		:show-more-url="'https://nextcloud.com'"
		:item-menu="itemMenu"
		@hide="onHide"
		@markDone="onMarkDone">

		<template #empty-content>
			Nothing to display
		</template>
	</DashboardWidget>
</template>

<script>
const myItems = [
	{
		id: '1',
		targetUrl: 'https://target.org',
		avatarUrl: 'https://avatar.url/img.png',
		avatarUsername: 'Robert',
		avatarIsNoUser: true,
		overlayIconUrl: '/svg/core/actions/sound?color=000',
		mainText: 'First item text',
		subText: 'First item subtext',
	},
	{
		id: '2',
		targetUrl: 'https://other-target.org',
		avatarUrl: 'https://other-avatar.url/img.png',
		overlayIconUrl: '/svg/core/actions/add?color=000',
		mainText: 'Second item text',
		subText: 'Second item subtext',
	},
]
const myItemMenu = {
	// triggers an event named "markDone" when clicked
	'markDone': {
		text: 'Mark as done',
		icon: 'icon-checkmark',
	},
	// triggers an event named "hide" when clicked
	'hide': {
		text: 'Hide',
		icon: 'icon-toggle',
	}
}
export default {
	name: 'MyDashboardWidget',
	props: [],
	data() {
		return {
			items: myItems,
			itemMenu: myItemMenu,
			loading: true,
		}
	},
	methods: {
		onMoreClick() {
			console.log('more clicked')
			const win = window.open('https://wherever.you.want', '_blank')
			win.focus()
		},
		onHide(item) {
			console.log('user wants to hide item ' + item.id)
			// do what you want
		},
		onMarkDone(item) {
			console.log('user wants to mark item ' + item.id + ' as done')
			// do what you want
		},
	},
}
</script>
```

</docs>

<template>
	<div>
		<!-- This element is shown if we have items, but want to show a general message as well.
		Can be used e.g. to show "No mentions" on top of the item list. -->
		<EmptyContent v-if="showHalfEmptyContentArea"
			class="half-screen"
			:icon="halfEmptyContentIcon">
			<template #desc>
				{{ halfEmptyContentString }}
			</template>
		</EmptyContent>
		<!-- The list of items to show. -->
		<ul>
			<li v-for="item in displayedItems" :key="item.id">
				<!-- @slot The default slot can be optionally overridden. It contains the template of one item. -->
				<slot name="default" :item="item">
					<DashboardWidgetItem v-bind="item"
						:item-menu="itemMenu"
						v-on="handlers" />
				</slot>
			</li>
		</ul>
		<!-- While the widget is loading, we show a list of placeholder items. -->
		<div v-if="loading">
			<div v-for="i in 7" :key="i" class="item-list__entry">
				<Avatar class="item-avatar" :size="44" />
				<div class="item__details">
					<h3>&nbsp;</h3>
					<p class="message">
&nbsp;
					</p>
				</div>
			</div>
		</div>
		<!-- @slot Slot for showing information in case of an empty item list. -->
		<slot v-else-if="items.length === 0" name="empty-content">
			<EmptyContent v-if="emptyContentMessage"
				:icon="emptyContentIcon">
				<template #desc>
					{{ emptyContentMessage }}
				</template>
			</EmptyContent>
		</slot>
		<!-- A "show more" link, e.g. to navigate to the main app belonging to this widget. -->
		<a v-else-if="showMore"
			:href="showMoreUrl"
			target="_blank"
			class="more"
			tabindex="0">
			{{ showMoreLabel }}
		</a>
	</div>
</template>

<script>
import Avatar from '../Avatar/index.js'
import DashboardWidgetItem from '../DashboardWidgetItem/index.js'
import EmptyContent from '../EmptyContent/index.js'

import { t } from '../../l10n.js'

export default {
	name: 'DashboardWidget',
	components: {
		Avatar,
		DashboardWidgetItem,
		EmptyContent,
	},

	props: {
		/**
		 * An array containing the items to show (specific structure must be respected,
		 * except if you override item rendering in the default slot).
		 */
		items: {
			type: Array,
			default: () => { return [] },
		},
		/**
		 * If this is set, a "show more" text is displayed on the widget's bottom.
		 * It's a link pointing to this URL.
		 */
		showMoreUrl: {
			type: String,
			default: '',
		},
		/**
		 * The type of elements to show more.
		 */
		showMoreText: {
			type: String,
			default: t('items'),
		},
		/**
		 * A boolean to put the widget in a loading state.
		 */
		loading: {
			type: Boolean,
			default: false,
		},
		/**
		 * An object containing context menu entries that will be displayed for each item.
		 */
		itemMenu: {
			type: Object,
			default: () => { return {} },
		},
		/**
		 * Whether both the items and the empty content message are shown.
		 * Usefull for e.g. showing "No mentions" and a list of elements.
		 */
		showItemsAndEmptyContent: {
			type: Boolean,
			default: false,
		},
		/**
		 * The icon to show in the empty content area.
		 */
		emptyContentIcon: {
			type: String,
			default: '',
		},
		/**
		 * The text to show in the empty content area.
		 */
		emptyContentMessage: {
			type: String,
			default: '',
		},
		/**
		 * The icon to show in the half empty content area.
		 */
		halfEmptyContentIcon: {
			type: String,
			default: 'icon-checkmark',
		},
		/**
		 * The text to show in the half empty content area.
		 */
		halfEmptyContentMessage: {
			type: String,
			default: '',
		},
	},

	computed: {
		// forward menu events to my parent
		handlers() {
			const h = {}
			for (const evName in this.itemMenu) {
				h[evName] = (it) => {
					this.$emit(evName, it)
				}
			}
			return h
		},
		displayedItems() {
			const nbItems = (this.showMoreUrl && this.items.length >= this.maxItemNumber)
				? this.maxItemNumber - 1
				: this.maxItemNumber
			return this.items.slice(0, nbItems)
		},

		showHalfEmptyContentArea() {
			return this.showItemsAndEmptyContent && this.halfEmptyContentString && this.items.length !== 0
		},

		halfEmptyContentString() {
			return this.halfEmptyContentMessage || this.emptyContentMessage
		},

		maxItemNumber() {
			return this.showItemsAndEmptyContent ? 5 : 7
		},

		showMore() {
			return this.showMoreUrl && this.items.length >= this.maxItemNumber
		},

		showMoreLabel() {
			return t('More {dashboardItemType} …', { dashboardItemType: this.showMoreText })
		},
	},
}
</script>

<style scoped lang="scss">
.empty-content {
	text-align: center;
	margin-top: 5vh;

	&.half-screen {
		margin-top: 0;
		margin-bottom: 1vh;
	}
}

.more {
	display: block;
	text-align: center;
	color: var(--color-text-maxcontrast);
	line-height: 60px;
	cursor: pointer;

	&:hover,
	&:focus {
		background-color: var(--color-background-hover);
		border-radius: var(--border-radius-large);
		color: var(--color-main-text);
	}
}

/* skeleton */
.item-list__entry {
	display: flex;
	align-items: flex-start;
	padding: 8px;

	.item-avatar {
		position: relative;
		margin-top: auto;
		margin-bottom: auto;
		background-color: var(--color-background-dark) !important;
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
			background-color: var(--color-background-dark);
		}
		h3 {
			font-size: 100%;
			margin: 0;
		}
		.message {
			width: 80%;
			height: 15px;
			margin-top: 5px;
		}
	}
}
</style>
