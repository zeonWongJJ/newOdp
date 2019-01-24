<template>
	<Card class="tableListGooo">
		
		<div class='ivu-table ivu-table-border ivu-table-stripe' id="printTable">
			<table border="1"  style="width:100%; border: 1px; table-layout:fixed;" >
				<colgroup >
					<col width="110">
					<col width="300">
					<col width="300" >
					<col width="150">
					<col width="60">
				</colgroup>
				<tr class="ivu-table-row">
					<th class="ivu-table-column-center">
						<div class="ivu-table-cell">
							<span class="tableTit">参与人</span>
						</div>
					</th>
					<th class="ivu-table-column-center">
						<div class="ivu-table-cell">
							<span class="tableTit">计划</span>
						</div>
					</th>
					<th class="ivu-table-column-center">
						<span class="tableTit">进度说明及问题原因</span>
					</th>
					<th class="ivu-table-column-center">
						<div class="ivu-table-cell">
							<span class="tableTit">起止时间</span>
						</div>
					</th>
					<th class="ivu-table-column-center">
						<div class="ivu-table-cell">
							<span class="tableTit">进度</span>
						</div>
					</th>
				</tr>
				<template v-for="(plan, index) in planData">
					<tr class="ivu-table-row">
						<!--<td  class="ivu-table-column-center" :rowspan="plan.group_plan_list.length " >
							<pre>{{plan.name}}</pre>
						</td>-->
						<td  class="ivu-table-column-center" >
							{{plan.name}}
						</td>
						<td class="ivu-table-column-center">{{plan.plan_name}}</td>
						<td class="ivu-table-column-center"  v-if="plan.content == '' && plan.progress ==0">计划尚未开始！</td>
						<td class="ivu-table-column-center"  v-else>{{plan.content}}</td>
						<td class="ivu-table-column-center">{{plan.date}}</td>
						<td class="ivu-table-column-center">{{plan.progress}}</td>
					</tr>
					<!--<tr class="ivu-table-row" v-for="(planItem, index) in plan.group_plan_list" v-if="index">
						<td class="ivu-table-column-center">{{planItem.plan_name}}</td>
						<td class="ivu-table-column-center" style="word-wrap:break-word;">
							<div>
								{{planItem.plan_discuss}}
							</div>
						</td>
						<td class="ivu-table-column-center">{{planItem.date}}</td>
						<td class="ivu-table-column-center" v-if="planItem.progress !=null">{{planItem.progress}}%</td>
						<td class="ivu-table-column-center" v-else>0%</td>
					</tr>-->
				</template>
			
		</table>
		</div>
		
	</Card>
</template>

<script>
	export default {
		name: 'dataTable',
		props: {
			planData: {
				type: Object,
				default () {
					return {}
				}
			}
		},
		data(){
			return{
				monthShow:false,
			}
		},
		mounted () {
			
		},
		methods: {
			//跳转到对应计划
			goPlan(plan,index){
				this.$store.commit('sub_id', plan.plan_sub_id);
				console.log(plan.plan_sub_id,'asda ')
				this.$router.push({path:'/plan_list_x'})
			}
		}
	}
</script>

<style scoped>
	.tableTit{
		font-size:14px;
		font-weight: bold;
	}

</style>