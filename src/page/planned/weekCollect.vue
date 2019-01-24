<template>
	<div class="weekContainer">

		<div class="weekContent">
			<div style="text-align:right; padding:10px 0; background: #fff;">
				<!--<DatePicker type="month" placeholder="Select month" style="width: 200px"></DatePicker>-->
				<Button type="info" @click="prevWeek()">上周</Button>
				<Button type="info" @click="thisWeek()">本周</Button>
				<Button type="info" @click="nextWeek()">下周</Button>
				<Button type="success" v-print="'#printTable'">打印</Button>
				<Button type="success" @click="export2Excel">导出excel</Button>
			</div>
			<div style="background:#fff; padding:10px; text-align: center; font-size:14px">{{start}}日&nbsp;~&nbsp;{{end}}日</div>
			<dataTable :planData="planData"></dataTable>
			<!--<Table highlight-row :align="center" ref="currentRowTable" :stripe="true" :border="true" :columns="columns3" :data="data1"></Table>-->
		</div>
	</div>
</template>

<script>
	import api from '@/api/api'
	import dataTable from './dataTable'

	export default {
		data() {
			return {
				currentFirstDate: '',
				columns3: [{
						type: 'index',
						width: 60,
						align: 'center'
					},
					{
						title: '计划',
						key: 'plan_name'
					},
					{
						title: '动作记录',
						key: 'plan_discuss'
					},
					{
						title: '时间',
						key: 'date'
					},
					{
						title: '进度',
						key: 'progress',
						render: (h, param) => {
							return h('span', `${param.row.progress}%`)
						}
					},
				],
				data1: [],
				planData: [],
				weekStartDate: '',
				weekEndDate: '',
				nowDate: new Date(),
				start: '',
				end: '',

			}
		},
		components: {
			dataTable
		},
		mounted() {
			this.setDate(new Date());
			this.thisWeek()
		},
		methods: {
			export2Excel() {　　　　　　
				require.ensure([], () => {　　　　　　　　
					const {
						export_json_to_excel
					} = require('../vendor/Export2Excel');　　　　　　　　
					const tHeader = ['参与人', '计划', '进度说明及问题原因', '起止时间', '进度'];　
					const filterVal = ['name', 'plan_name', 'content', 'date', 'progress'];
					const list = this.planData;　　　　　　　　
					const data = this.formatJson(filterVal, list);　　　　　　　　
					export_json_to_excel(tHeader, data, 'odp' + this.weekStartDate + '-' + this.weekEndDate);　　　　　　
				})　　　　
			},
			formatJson(filterVal, jsonData) {　　　　　　
				return jsonData.map(v => filterVal.map(j => v[j]))　　　　
			},
			handleClearCurrentRow() {
				this.$refs.currentRowTable.clearCurrentRow();
			},

			plan() {
				this.$router.push({
					path: 'plan_x'
				})
			},
			participate_plan() {
				this.$router.push({
					path: 'company_plan_x'
				})
			},
			addDate(date, n) {
				date.setDate(date.getDate() + n);
				return date;
			},
			formatDate(date) {
				var year = date.getFullYear() + '-';
				var month = (date.getMonth() + 1) + '-';
				var yearS = date.getFullYear() + '年';
				var monthS = (date.getMonth() + 1) + '月';
				var day = date.getDate() ;
				const index = date.getDay()
				var thisWeekStartDate = year + month + day
				if(index === 1) {
					//		        	console.log(new Date(year+month+day+' 00:00'))
					this.weekStartDate = year + month + day
					console.log(thisWeekStartDate);
					if(this.weekStartDate  ){
						
					}
//					if( this.weekStartDate === date.setHours(0,0,0,0) ){
//						console.log(date.setHours(0,0,0,0))
//					}else{
//						console.log(day)
//					}
					this.start = yearS + monthS + day
				} else if(index === 5) {
//					day=day+1
					this.weekEndDate = year + month + day
					this.end = yearS + monthS + day
				}
			},
			setDate(date) {
				var week = date.getDay() - 1;
				date = this.addDate(date, week * -1);
				this.currentFirstDate = new Date(date);
				for(var i = 0; i < 8; i++) {
					//		           this.formatDate(i==0 ? date : this.addDate(date,1));    //星期一开始
					this.formatDate(i == 0 ? this.addDate(date, -1) : this.addDate(date, 1)); //星期日开始
				}
			},
			_formatDate(date1, date2) {
				let that = this;
				let list = {};
				list.start_date = date1 + " 00:00:00"
				list.end_date = date2 + " 23:59:59"
				list.rows = 500
				var qs = require('qs');
				that.axios({
					method: 'post',
					url: api.plan_weekly,
					data: qs.stringify(list) //传参变量
				}).then(rs => {
					if(rs.data.error === 0) {
						this.planData = rs.data.data.list
						this.planData.forEach(item => {
							item.content == '' ? item.content = "计划尚未开始！" : 1
							item.progress += "%"
						})
						//							row.push(rs.data.data.list);
					}
					//					this.data1=row
				})
			},
			//上周
			prevWeek() {
				this.setDate(this.addDate(this.currentFirstDate, -7))
				this._formatDate(this.weekStartDate, this.weekEndDate)
			},
			//本周
			thisWeek() {
				this.setDate(new Date());
				this.setDate(this.addDate(this.currentFirstDate, 0))
				this._formatDate(this.weekStartDate, this.weekEndDate)
			},
			//下周
			nextWeek() {
				this.setDate(this.addDate(this.currentFirstDate, 7))
				this._formatDate(this.weekStartDate, this.weekEndDate)
			}
		}
	}
</script>

<style scoped>
	.weekContainer {
		width: 100%;
		height: 100%;
		overflow-y: scroll;
		/*height:calc(100% - 1px ) ;*/
		/*overflow: scroll;*/
	}
	
	.week_content {}
	
	.tit_box {
		position: absolute;
		top: -88px;
		left: 0px;
		height: 85px;
		margin: 0 0 0 50px;
		border-bottom: 1px solid rgba(186, 233, 249, .5);
		z-index: 999;
	}
	
	.plan_tit_box_ul {
		display: flex;
		width: 800px;
		margin: 0 auto;
	}
	
	.plan_tit_box_ul li {
		text-align: center;
		font-size: 18px;
		width: 350px;
		line-height: 85px;
		height: 85px;
		position: relative;
	}
	
	.plan_tit_box_ul li:hover:before {
		content: "";
		position: absolute;
		bottom: 0;
		left: 88px;
		height: 2px;
		width: 100px;
		background-color: #0ab3e9;
	}
	
	.tit_box_li {
		color: #0ab3e9;
	}
	
	.tit_box_li:before {
		content: "";
		position: absolute;
		bottom: 0;
		left: 88px;
		height: 2px;
		width: 100px;
		background-color: #0ab3e9;
	}
</style>