<!--
 - @copyright Copyright (c) 2018 Christoph Wurst <christoph@winzerhof-wurst.at>
 -
 - @author Christoph Wurst <christoph@winzerhof-wurst.at>
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
### Usage

 ```
 <template>
	<AppNavigationNew text="New Element">
		<template #icon>
			<Plus :size="20" decorative />
		</template>
	</AppNavigationNew>
 </template>
 <script>
 import Plus from 'vue-material-design-icons/Plus'

 export default {
	components: {
		Plus,
	},
 }
 </script>
 ```
 </docs>

<template>
	<div class="app-navigation-new">
		<Button :id="buttonId"
			:disabled="disabled"
			@click="$emit('click')">
			<template #icon>
				<slot name="icon" />
			</template>
			{{ text }}
		</Button>
	</div>
</template>

<script>
import Button from '../Button/index.js'

export default {
	components: {
		Button,
	},
	props: {
		buttonId: {
			type: String,
			required: false,
			default: '',
		},
		disabled: {
			type: Boolean,
			required: false,
			default: false,
		},
		text: {
			type: String,
			required: true,
		},
	},
}
</script>

 <style lang="scss" scoped>
/* 'New' button */
.app-navigation-new {
	display: block;
	padding: 10px;
	button {
		width: 100%;
	}
}
</style>
