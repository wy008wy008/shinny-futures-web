<template>
	<div class="quotes-container" style="height: 100%">
<!--	split控制左右比例，之前是0.8，为了全屏改为1	-->
		<SplitLeftRight :split="1">
			<template slot="left">
				<div class="top-content">
					<ButtonsLine :tags="tags" :defaultTag="defaultTag" v-on:change="handlerChangeTag"></ButtonsLine>
				</div>
				<div class="bottom-content" style="top: 28px;">
					<table-quotes :contentList="quotesList" :tableCol="quotesTableRow" :rootPath="rootPath"/>
				</div>
			</template>
			<template slot="right">
				<div class="top-content">
					<quote-info :instrumentId="instrumentId"/>
				</div>
				<div class="bottom-content" style="top: 220px;">
					<ticks-list :instrumentId="instrumentId"/>
				</div>
			</template>
		</SplitLeftRight>
	</div>
</template>
<script>
	import {mapGetters} from 'vuex'
	import TableQuotes from '@/components/Table/Table.vue'
	import QuoteInfo from '@/components/QuoteInfo.vue'
	import TicksList from '@/components/TicksList.vue'
	import SplitLeftRight from '@/components/SplitLeftRight.vue'
	import ButtonsLine from '@/components/ButtonsLine.vue'

	import {QuotesTableRow} from '@/config'

	export default {
		name: 'quotes',
		components: {
			TableQuotes,
			QuoteInfo,
			TicksList,
			SplitLeftRight,
			ButtonsLine
		},
		data() {
			let defaultTag = this.$route.params.tag // 只初始化一次就不再改变
			return {
				defaultTag,
				tags: this.$store.state.tags,
				quotesTableRow: QuotesTableRow,
				rootPath: 'quotes/',
				selectedTab: 'quoteInfo' // chart ticks
			}
		},
		computed: {
			quotesList: function () {
				let list = this.$store.state.tagsQuotesMap[this.$route.params.tag]
				if (list) {
					this.$tqsdk.subscribe_quote(list)
				}
				return list
			},
			rowWidth: function () {
				let lengthOfWords = this.$store.state.tags.join('').length
				let lengthOfTags = this.$store.state.tags.length
				return lengthOfWords * 12.5 + lengthOfTags * 20
			},
			...mapGetters({
				instrumentId: 'getSelectedShowInstrumentId'
			})
		},
		methods: {
			handlerChangeTag: function (tag) {
				this.$router.replace({name: 'quotes', params: {tag}})
			}
		}
	}
</script>
<style scoped lang="scss">
	.quotes-container {
		.top-content {
			width: 100%;
		}

		.bottom-content {
			width: 100%;
			position: absolute;
			bottom: 0;
			overflow: hidden;
		}

		.row {
			height: 26px;
			overflow-x: hidden;
			overflow-y: hidden;
			.col {
				height: 28px;
				display: inline-block;
				padding: 0px 4px;
			}
		}
	}

</style>
