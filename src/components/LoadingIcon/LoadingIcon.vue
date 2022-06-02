<!--
 - @copyright Copyright (c) 2022 Raimund Schlüßler <raimund.schluessler@mailbox.org>
 -
 - @author Raimund Schlüßler <raimund.schluessler@mailbox.org>
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
	<Loading :size="size" />
</template>

<script>
import Loading from 'vue-material-design-icons/Loading'

export default {
	name: 'LoadingIcon',
	components: {
		Loading,
	},
	props: {
		size: {
			type: Number,
			default: 20,
		},
	},
}
</script>

<style lang="scss" scoped>
.material-design-icon::v-deep svg {
	animation-iteration-count: infinite;
	animation-duration: 1s;
	animation-name: spin;
}
@keyframes spin {
	0% {
		transform: rotate(0deg);
	}
	100% {
		transform: rotate(360deg);
	}
}
</style>
