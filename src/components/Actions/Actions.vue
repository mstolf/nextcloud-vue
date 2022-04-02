<!--
  - @copyright Copyright (c) 2018 John Molakvoæ <skjnldsv@protonmail.com>
  -
  - @author John Molakvoæ <skjnldsv@protonmail.com>
  - @author Marco Ambrosini <marcoambrosini@pm.me
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

<!-- Accessibility guidelines:
https://www.w3.org/TR/wai-aria-practices/examples/menu-button/menu-button-actions.html -->

<docs>
### Single action

```
<Actions>
	<ActionButton icon="icon-delete" @click="alert('Delete')">Delete</ActionButton>
</Actions>
```

### Multiple actions

```
<Actions>
	<ActionButton icon="icon-edit" @click="alert('Edit')">Edit</ActionButton>
	<ActionButton icon="icon-delete" @click="alert('Delete')">Delete</ActionButton>
	<ActionLink icon="icon-external" title="Link" href="https://nextcloud.com" />
</Actions>
```

### Multiple actions with custom icon

```
<Actions default-icon="icon-edit">
	<ActionButton icon="icon-edit" @click="alert('Edit')">Edit</ActionButton>
	<ActionButton icon="icon-delete" @click="alert('Delete')">Delete</ActionButton>
	<ActionLink icon="icon-external" title="Link" href="https://nextcloud.com" />
</Actions>
```

### With menu title

```
<Actions default-icon="icon-edit" menu-title="Object management">
	<ActionButton icon="icon-edit">Rename</ActionButton>
	<ActionButton icon="icon-delete">Delete</ActionButton>
	<ActionButton icon="icon-confirm">Validate</ActionButton>
	<ActionButton icon="icon-download">Download</ActionButton>
</Actions>
```

### Various icons styles
```
<Actions :primary="true">
	<ActionButton icon="icon-edit">Edit</ActionButton>
	<ActionButton icon="icon-delete">Delete</ActionButton>
</Actions>
```

```
<Actions default-icon="icon-add-white" :primary="true" menu-title="Object management">
	<ActionButton icon="icon-edit">Rename</ActionButton>
	<ActionButton icon="icon-delete">Delete</ActionButton>
	<ActionButton icon="icon-confirm">Validate</ActionButton>
	<ActionButton icon="icon-download">Download</ActionButton>
</Actions>
```

### Custom icon slot
To be used with `vue-material-design-icons` only. For icon classes use the `default-icon` slot.
It can be used with one or multiple actions.
```
<template>
	<div style="display: flex;align-items: center;">
		<button @click="toggled = !toggled">Toggle multiple action</button>
		<Actions>
			<template #icon>
				<DotsHorizontalCircleOutline :size="20" decorative />
			</template>
			<ActionButton>
				<template #icon>
					<MicrophoneOff :size="20" decorative />
				</template>
				Mute
			</ActionButton>
			<ActionButton v-if="toggled" icon="icon-delete">Delete</ActionButton>
		</Actions>
		<Actions>
		</Actions>
	</div>
</template>
<script>
import DotsHorizontalCircleOutline from 'vue-material-design-icons/DotsHorizontalCircleOutline'
import MicrophoneOff from 'vue-material-design-icons/MicrophoneOff'

export default {
	components: {
		DotsHorizontalCircleOutline,
		MicrophoneOff,
	},
	data() {
		return {
			toggled: false
		}
	}
}
</script>
```

### Custom icon slot in child elements
```
<Actions :primary="true">
	<ActionButton><template #icon><MagnifyIcon /></template>Search</ActionButton>
	<ActionButton icon="icon-delete">Delete</ActionButton>
</Actions>
```

</docs>
<template>
	<!-- if only one action, check if we need to bind to click or not -->
	<component :is="firstActionTag"
		v-if="isValidSingleAction && !forceMenu"
		v-tooltip.auto="firstActionText"
		v-bind="{...$attrs, ...actions[0].props}"
		:class="{
			'action-item--single--with-title': singleActionTitle }"
		class="action-item action-item--single"
		rel="nofollow noreferrer noopener"
		:disabled="isDisabled"
		@focus="onFocus"
		@blur="onBlur">
		<!-- Render the icon slot content of the first action -->
		<VNodes :vnodes="firstActionIconSlot" />

		{{ singleActionTitle }}

		<!-- fake slot to gather main action -->
		<span :aria-hidden="true" hidden>
			<!-- @slot All action elements passed into the default slot will be used -->
			<slot />
		</span>
	</component>

	<!-- more than one action -->
	<div v-else
		v-show="hasMultipleActions || forceMenu"
		:class="{'action-item--open': opened}"
		class="action-item">
		<!-- If more than one action, create a popovermenu -->
		<Popover v-model:shown="opened"
			:delay="0"
			:handle-resize="true"
			:placement="placement"
			:boundary="boundariesElement"
			:container="container"
			@show="openMenu"
			@after-show="onOpen"
			@hide="closeMenu">
			<!-- Menu open/close trigger button -->
			<template #trigger>
				<button ref="menuButton"
					:disabled="disabled"
					class="icon vue-button action-item__menutoggle"
					:class="{
						[defaultIcon]: !iconSlotIsPopulated,
						'action-item__menutoggle--with-title': menuTitle,
						'action-item__menutoggle--with-icon-slot': iconSlotIsPopulated,
						'action-item__menutoggle--default-icon': !iconSlotIsPopulated && defaultIcon === '',
						'action-item__menutoggle--primary': primary
					}"
					aria-haspopup="true"
					:aria-label="ariaLabel"
					:aria-controls="randomId"
					:aria-expanded="opened ? 'true' : 'false'"
					test-attr="1"
					type="button"
					@focus="onFocus"
					@blur="onBlur">
					<slot v-if="iconSlotIsPopulated" name="icon" />
					<DotsHorizontal v-else-if="defaultIcon === ''" :size="20" decorative />
					{{ menuTitle }}
				</button>
			</template>

			<!-- Menu content -->
			<div v-show="opened"
				ref="menu"
				:class="{ open: opened }"
				tabindex="-1"
				@keydown.up.exact="focusPreviousAction"
				@keydown.down.exact="focusNextAction"
				@keydown.tab.exact="focusNextAction"
				@keydown.shift.tab.exact="focusPreviousAction"
				@keydown.page-up.exact="focusFirstAction"
				@keydown.page-down.exact="focusLastAction"
				@keydown.esc.exact.prevent="closeMenu"
				@mousemove="onMouseFocusAction">
				<!-- menu content -->
				<ul :id="randomId" tabindex="-1">
					<template v-if="opened">
						<slot />
					</template>
				</ul>
			</div>
		</Popover>
	</div>
</template>
<script>
import Popover from '../Popover/index.js'
import VNodes from '../VNodes/index.js'
import Tooltip from '../../directives/Tooltip/index.js'
import GenRandomId from '../../utils/GenRandomId.js'
import { t } from '../../l10n.js'

import DotsHorizontal from 'vue-material-design-icons/DotsHorizontal'

import { Fragment } from 'vue'

const focusableSelector = '.focusable'

/**
 * The Actions component can be used to display one ore more actions.
 * If only a single action is provided, it will be rendered as an inline icon.
 * For more, a menu indicator will be shown and a popovermenu containing the
 * actions will be opened on click.
 *
 * @since 0.10.0
 */
export default {
	name: 'Actions',

	directives: {
		tooltip: Tooltip,
	},

	components: {
		DotsHorizontal,
		Popover,

		// Component to render the first action icon slot content (vnodes)
		VNodes,
	},

	props: {
		/**
		 * Specify the open state of the popover menu
		 */
		open: {
			type: Boolean,
			default: false,
		},

		/**
		 * Force the actions to display in a three dot menu
		 */
		forceMenu: {
			type: Boolean,
			default: false,
		},

		/**
		 * Force the title to show for single actions
		 */
		forceTitle: {
			type: Boolean,
			default: false,
		},

		/**
		 * Specify the menu title
		 */
		menuTitle: {
			type: String,
			default: null,
		},

		/**
		 * Apply primary styling for this menu
		 */
		primary: {
			type: Boolean,
			default: false,
		},

		/**
		 * Icon to show for the toggle menu button
		 * when more than one action is inside the actions component.
		 * Only replace the default three-dot icon if really necessary.
		 */
		defaultIcon: {
			type: String,
			default: '',
		},

		/**
		 * Aria label for the actions menu
		 */
		ariaLabel: {
			type: String,
			default: t('Actions'),
		},

		/**
		 * Wanted direction of the menu
		 */
		placement: {
			type: String,
			default: 'bottom',
		},

		/**
		 * DOM element for the actions' popover boundaries
		 */
		boundariesElement: {
			type: Element,
			default: () => document.querySelector('body'),
		},

		/**
		 * Selector for the actions' popover container
		 */
		container: {
			type: String,
			default: 'body',
		},

		/**
		 * Disabled state of the main button (single action or menu toggle)
		 */
		disabled: {
			type: Boolean,
			default: false,
		},
	},

	emits: [
		'open',
		'update:open',
		'close',
		'focus',
		'blur',
	],

	data() {
		return {
			actions: [],
			opened: this.open,
			focusIndex: 0,
			randomId: 'menu-' + GenRandomId(),
		}
	},

	computed: {
		/**
		 * Is there more than one action?
		 *
		 * @return {boolean}
		 */
		hasMultipleActions() {
			return this.actions.length > 1
		},
		/**
		 * Is there any first action ?
		 * And is it allowed as a standalone element ?
		 *
		 * @return {boolean}
		 */
		isValidSingleAction() {
			return this.actions.length === 1
				&& this.actions[0]?.type?.name !== null
		},
		/**
		 * Return the title of the single action if forced
		 *
		 * @return {string}
		 */
		singleActionTitle() {
			return this.forceTitle ? this.menuTitle : ''
		},
		/**
		 * Whether the Actions are disabled.
		 *
		 * @return {boolean}
		 */
		isDisabled() {
			return this.disabled
				|| (this.isValidSingleAction && this.actions[0]?.props?.disabled)
		},
		/**
		 * The tag of the first action in case only one action is given.
		 *
		 * @return {string}
		 */
		firstActionTag() {
			const name = this.actions[0]?.type?.name
			if (name === 'ActionLink') {
				return 'a'
			}
			if (name === 'ActionRouter') {
				return 'vue:router-link'
			}
			if (name === 'ActionButton') {
				return 'button'
			}
			// other action types are not allowed as standalone buttons
			return null
		},
		/**
		 * The text of the first action to show in a tooltip for a single action.
		 *
		 * @return {string}
		 */
		firstActionText() {
			return this.actions[0]?.children?.default?.()[0].children?.trim?.()
		},

		// return the first action icon slot vnodes array
		firstActionIconSlot() {
			return this.actions[0]?.children?.icon?.()?.find(vnode => typeof vnode?.type !== 'symbol')
		},

		iconSlotIsPopulated() {
			return this.$slots.icon?.().filter(
				(item) => (typeof item?.type) !== 'symbol'
			)?.length > 0
		},
	},

	watch: {
		// Watch parent prop
		open(state) {
			if (state === this.opened) {
				return
			}

			this.opened = state
		},
	},
	beforeMount() {
		// init actions
		this.initActions()
	},
	beforeUpdate() {
		// ! since we're using $slots to manage our actions
		// ! we NEED to update the actions if anything change
		// update children & actions
		// no need to init actions again since we bound it to $children
		// and the array is now reactive
		// init actions
		this.initActions()
	},

	methods: {
		// MENU STATE MANAGEMENT
		openMenu(e) {
			if (this.opened) {
				return
			}

			this.opened = true

			/**
			 * Event emitted when the popover menu open state is changed
			 *
			 * @type {boolean}
			 */
			this.$emit('update:open', true)

			/**
			 * Event emitted when the popover menu is opened
			 */
			this.$emit('open')
		},
		closeMenu(e) {
			if (!this.opened) {
				return
			}

			this.opened = false

			/**
			 * Event emitted when the popover menu open state is changed
			 *
			 * @type {boolean}
			 */
			this.$emit('update:open', false)

			/**
			 * Event emitted when the popover menu is closed
			 */
			this.$emit('close')

			// close everything
			this.opened = false
			this.focusIndex = 0

			// focus back the menu button
			this.$refs.menuButton.focus()
		},

		onOpen(event) {
			this.$nextTick(() => {
				this.focusFirstAction(event)
			})
		},

		// MENU KEYS & FOCUS MANAGEMENT
		// focus nearest focusable item on mouse move
		// DO NOT change the focus if the target is already focused
		// this will prevent issues with input being unfocused
		// on mouse move
		onMouseFocusAction(event) {
			if (document.activeElement === event.target) {
				return
			}

			const menuItem = event.target.closest('li')
			if (menuItem) {
				const focusableItem = menuItem.querySelector(focusableSelector)
				if (focusableItem) {
					const focusList = this.$refs.menu.querySelectorAll(focusableSelector)
					const focusIndex = [...focusList].indexOf(focusableItem)
					if (focusIndex > -1) {
						this.focusIndex = focusIndex
						this.focusAction()
					}
				}
			}
		},
		removeCurrentActive() {
			const currentActiveElement = this.$refs.menu.querySelector('li.active')
			if (currentActiveElement) {
				currentActiveElement.classList.remove('active')
			}
		},
		focusAction() {
			// TODO: have a global disabled state for non input elements
			const focusElement = this.$refs.menu.querySelectorAll(focusableSelector)[this.focusIndex]
			if (focusElement) {
				this.removeCurrentActive()
				const liMenuParent = focusElement.closest('li.action')
				focusElement.focus()
				if (liMenuParent) {
					liMenuParent.classList.add('active')
				}
			}
		},
		focusPreviousAction(event) {
			if (this.opened) {
				if (this.focusIndex === 0) {
					// First element overflows to body-navigation (no preventDefault!) and closes Actions-menu
					this.closeMenu()
				} else {
					this.preventIfEvent(event)
					this.focusIndex = this.focusIndex - 1
				}
				this.focusAction()
			}
		},
		focusNextAction(event) {
			if (this.opened) {
				const indexLength = this.$refs.menu.querySelectorAll(focusableSelector).length - 1
				if (this.focusIndex === indexLength) {
					// Last element overflows to body-navigation (no preventDefault!) and closes Actions-menu
					this.closeMenu()
				} else {
					this.preventIfEvent(event)
					this.focusIndex = this.focusIndex + 1
				}
				this.focusAction()
			}
		},
		focusFirstAction(event) {
			if (this.opened) {
				this.preventIfEvent(event)
				this.focusIndex = 0
				this.focusAction()
			}
		},
		focusLastAction(event) {
			if (this.opened) {
				this.preventIfEvent(event)
				this.focusIndex = this.$el.querySelectorAll(focusableSelector).length - 1
				this.focusAction()
			}
		},

		preventIfEvent(event) {
			if (event) {
				event.preventDefault()
				event.stopPropagation()
			}
		},
		/**
		 * This returns all actions in the default slot.
		 *
		 * @return {object}
		 */
		initActions() {
			const actions = []
			// We have to iterate over all slot elements
			this.$slots.default?.().forEach(vnode => {
				if (this.isAction(vnode)) {
					actions.push(vnode)
					return
				}
				// If we encounter a Fragment, we have to check its children too
				if (vnode?.type === Fragment) {
					vnode?.children?.forEach?.(child => {
						if (this.isAction(child)) {
							actions.push(child)
							return
						}
					})
				}

			})
			this.actions = actions
		},
		isAction(vnode) {
			return vnode?.type?.name?.startsWith?.('Action')
		},
		onFocus(event) {
			this.$emit('focus', event)
		},
		onBlur(event) {
			this.$emit('blur', event)
		},
	},
}
</script>

<style lang="scss" scoped>
.action-item {
	position: relative;
	display: inline-block;

	// put a grey round background when menu is opened
	// or hover-focused
	&--single:hover,
	&--single:focus,
	&--single:active,
	&__menutoggle:hover,
	&__menutoggle:focus,
	&__menutoggle:active {
		opacity: $opacity_full;
		// good looking on dark AND white bg, override server styling
		background-color: $icon-focus-bg !important;
	}

	// TODO: handle this in the future button component
	&__menutoggle:disabled,
	&--single:disabled {
		opacity: .3 !important;
	}

	&.action-item--open .action-item__menutoggle {
		opacity: $opacity_full;
		background-color: $action-background-hover;
	}

	// icons
	&--single,
	&__menutoggle {
		box-sizing: border-box;
		width: auto;
		min-width: $clickable-area;
		height: $clickable-area;
		margin: 0;
		padding: 0;
		cursor: pointer;
		border: none;
		border-radius: math.div($clickable-area, 2);
		background-color: transparent;

		&--with-title {
			position: relative;
			padding: 0 $icon-margin;
			padding-left: $clickable-area;
			white-space: nowrap;
			opacity: $opacity_full;
			border: 1px solid var(--color-border-dark);
			// with a title, we need to display this as a real button
			background-color: var(--color-background-dark);
			background-position: $icon-margin center;
			font-size: inherit;

			// non-background icon class
			// image slot
			:deep(span) {
				width: 24px;
				height: 24px;
				line-height: $icon-size;
				position: absolute;
				top: 0;
				left: 0;
			}
		}
	}

	&:deep(.material-design-icon) {
		width: $clickable-area;
		height: $clickable-area;
		opacity: $opacity_full;

		.material-design-icon__svg {
			vertical-align: middle;
		}
	}

	// icon-more
	&__menutoggle {
		// align menu icon in center
		display: flex;
		align-items: center;
		justify-content: center;
		opacity: $opacity_normal;
		font-weight: bold;
		line-height: $icon-size;

		&--primary {
			opacity: $opacity_full;
			color: var(--color-primary-text);
			border: none;
			background-color: var(--color-primary-element);
			.action-item--open &,
			&:hover,
			&:focus,
			&:active {
				color: var(--color-primary-text) !important;
				background-color: var(--color-primary-element-light) !important;
			}
		}
	}

	&--single {
		opacity: $opacity_normal;
		&:hover,
		&:focus,
		&:active {
			opacity: $opacity_full;
		}
		// hide anything the slot is displaying
		& > [hidden] {
			display: none;
		}
	}
}

.ie,
.edge {
	.action-item__menu,
	.action-item__menu .action-item__menu_arrow {
		border: 1px solid var(--color-border);
	}
}

</style>
