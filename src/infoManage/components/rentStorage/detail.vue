<template>
	<div class="detail">
		<div class="toolbar">
			<div class="btn-box">
				<el-button type="text" icon="mo-newlyAdded" @click.native="addRentStorage('form')">新增</el-button>
			</div>
			<div class="btn-box">
				<el-button type="text" icon="mo-activateSave" @click.native="saveRentStorage('form')">保存</el-button>
			</div>
		</div>
		<div class="common-condition-box">
			<el-form ref="form" :model="form" :rules="rules">
				<div class="condition-item">
					<common-row>
						<common-col>
							<span class="label required">运营商</span>
							<div class="content">
								<el-form-item prop="Operator_Name">
									<el-input placeholder="双击选择运营商" :disabled="true" @dblclick.native="openDialog('operator')" v-model="form.Operator_Name"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label required">委托方</span>
							<div class="content">
								<el-form-item prop="Consignor_Name">
									<el-input placeholder="双击选择委托方" :disabled="true" @dblclick.native="openDialog('consignor')" v-model="form.Consignor_Name"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label required">物流中心</span>
							<div class="content">
								<el-form-item prop="Ldc_Name">
									<el-input placeholder="双击选择物流中心" :disabled="true" @dblclick.native="openDialog('ldc')" v-model="form.Ldc_Name"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label">租赁面积</span>
							<div class="content">
								<el-form-item prop="Rental_Area">
									<el-input type="number" placeholder="请输入租赁面积" v-model="form.Rental_Area"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label required">租赁仓库存储条件</span>
							<div class="content">
								<el-form-item prop="Refrigeration_Type">
									<el-select v-model="form.Refrigeration_Type" clearable placeholder="请选择">
								    <el-option
								      v-for="item in refrigerationTypes" :key="item.Value_Data" :label="item.Value_Desc" :value="item.Value_Data">
								    </el-option>
								  </el-select>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label">租赁托盘货位数(立库)</span>
							<div class="content">
								<el-form-item prop="Rental_Location_A">
									<el-input type="number" placeholder="请输入租赁托盘货位数" v-model="form.Rental_Location_A"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label">租赁楼库货位数(整件)</span>
							<div class="content">
								<el-form-item prop="Rental_Location_B">
									<el-input type="number" placeholder="请输入租赁托盘货位数" v-model="form.Rental_Location_B"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label">租赁楼库货位(零货)</span>
							<div class="content">
								<el-form-item prop="Rental_Location_C">
									<el-input type="number" placeholder="请输入租赁托盘货位数" v-model="form.Rental_Location_C"></el-input>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label">租赁开始时间</span>
							<div class="content">
								<el-form-item prop="Start_Time">
									<el-date-picker v-model="form.Start_Time" type="date" placeholder="选择租赁开始时间"></el-date-picker>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<span class="label">租赁结束时间</span>
							<div class="content">
								<el-form-item prop="End_Time">
									<el-date-picker v-model="form.End_Time" type="date" placeholder="选择租赁结束时间"></el-date-picker>
								</el-form-item>
							</div>
						</common-col>
						<common-col>
							<el-form-item prop="Is_Active">
								<el-checkbox v-model="form.Is_Active">是否活动</el-checkbox>
							</el-form-item>
						</common-col>
					</common-row>
				</div>
			</el-form>
		</div>
		<common-modal ref="OperatorDialog" DialogTitle="运营方" :isVisible.sync="dialogShow.operator" :TableHeader="operator.TableHeader" :listData="operator.copyDatas" @confirm="selectOperator" @search="searchOperator" :isPages="false"></common-modal>
		<common-modal ref="ConDialog" DialogTitle="委托方" :isVisible.sync="dialogShow.consignor" :TableHeader="consignor.TableHeader" :listData="consignor.copyDatas" @confirm="selectConsignor" @search="searchConsignor" :isPages="false"></common-modal>
		<common-modal ref="LdcDialog" DialogTitle="物流中心" :isVisible.sync="dialogShow.ldc" :TableHeader="ldc.TableHeader" :listData="ldc.copyDatas" @confirm="selectLdc" @search="searchLdc" :isPages="false"></common-modal>
	</div>
</template>

<script>
	import Api from '@/common/js/api'
	import {DateFtt, Created32Guid} from '@/common/js/utils'
	import {Bus, Types} from '@/common/js/bus'
	import commonRow from '@/common/components/common-row'
	import commonCol from '@/common/components/common-col'
	import commonModal from '@/common/components/common-modal'
	export default {
		name: 'detail',
		data () {
			return {
				form: {
					Lease_Id: Created32Guid(32),
					Operator_Id: '',
					Operator_Name: '',
					Consignor_Id: '',
					Consignor_Name: '',
					Ldc_Id: '',
					Ldc_Name: '',
					Refrigeration_Type: '',
					Rental_Area: null,
					Rental_Location_A: null,
					Rental_Location_B: null,
					Rental_Location_C: null,
					Start_Time: '',
					End_Time: '',
					Is_Active: true,
					Creator: Api.userInfo.Staff_Id,
					Created_Time: DateFtt('yyyy-MM-dd hh:mm:ss', new Date(), false),
					Updated_Time: DateFtt('yyyy-MM-dd hh:mm:ss', new Date(), false),
					changetype: 'inserted'
				},
				dialogShow: {
					operator: false,
					consignor: false,
					ldc: false
				},
				rules: {
					Operator_Name: [{ required: true, message: '请选择运营方', trigger: 'blur' }],
					Consignor_Name: [{ required: true, message: '请选择委托方', trigger: 'blur' }],
					Ldc_Name: [{ required: true, message: '请选择物流中心', trigger: 'blur' }],
					Refrigeration_Type: [{ required: true, message: '请选择租赁仓库存储条件', trigger: 'blur' }]
				},
				emitDetailConsignor: this.consignor,
				emitDetailLdc: this.ldc
			}
		},
		props: {
			editRentStorageRow: {
				type: Object
			},
			refrigerationTypes: {
				type: Array
			},
			operator: {
				type: Object
			},
			consignor: {
				type: Object
			},
			ldc: {
				type: Object
			}
		},
		components: {
			commonRow,
			commonCol,
			commonModal
		},
		watch: {
			editRentStorageRow () {
				if (typeof (this.editRentStorageRow.Lease_Id) !== 'undefined') {
					this.setRentStorage(this.editRentStorageRow)
				} else {
					this.refreshDetail('form')
				}
			},
			'operator.datas': function (val) {
				if (val.length === 1) {
  				this.form.Operator_Id = val[0].Operator_Id
  				this.form.Operator_Name = val[0].Operator_Name
  				this.$emit('operator-select', this.form.Operator_Id, 'detail')
  			}
			}
		},
		methods: {
			openDialog (val) { // 打开弹框
        this.dialogShow[val] = true
      },
      selectOperator (row) {
				this.form.Operator_Id = row.Operator_Id
				this.form.Operator_Name = row.Operator_Name
			},
			searchOperator (keyword) {
				let searchRegex = new RegExp(keyword, 'i')
        this.operator.copyDatas = this.operator.datas.filter((item) => {
          for (let key in item) {
            if (searchRegex.test(item[key])) {
              return true
            }
          }
        })
			},
			selectConsignor (row) {
				this.form.Consignor_Id = row.Con_Id
				this.form.Consignor_Name = row.Con_Name
			},
			searchConsignor (keyword) {
				let searchRegex = new RegExp(keyword, 'i')
        this.emitDetailConsignor.copyDatas = this.emitDetailConsignor.datas.filter((item) => {
          for (let key in item) {
            if (searchRegex.test(item[key])) {
              return true
            }
          }
        })
			},
			selectLdc (row) {
				this.form.Ldc_Id = row.Ldc_Id
				this.form.Ldc_Name = row.Ldc_Name
			},
			searchLdc (keyword) {
				let searchRegex = new RegExp(keyword, 'i')
        this.emitDetailLdc.copyDatas = this.emitDetailLdc.datas.filter((item) => {
          for (let key in item) {
            if (searchRegex.test(item[key])) {
              return true
            }
          }
        })
			},
      refreshDetail (formName) { // 刷新明细
      	this.$refs[formName].resetFields()
      	if (this.operator.datas.length === 1) {
  				this.form.Operator_Id = this.operator.datas[0].Operator_Id
  				this.form.Operator_Name = this.operator.datas[0].Operator_Name
  			}
      },
			addRentStorage (formName) {
				this.$refs[formName].resetFields()
				if (this.operator.datas.length === 1) {
  				this.form.Operator_Id = this.operator.datas[0].Operator_Id
  				this.form.Operator_Name = this.operator.datas[0].Operator_Name
  			}
			},
			setRentStorage (obj) { // 编辑仓储租赁规则
				let isActive = true
				if (obj.Is_Active === 'N') {
					isActive = false
				}
				this.form.Lease_Id = obj.Lease_Id
				this.form.Operator_Id = obj.Operator_Id
				this.form.Operator_Name = obj.Operator_Name
				this.form.Consignor_Id = obj.Con_Id
				this.form.Consignor_Name = obj.Con_Name
				this.form.Ldc_Id = obj.Ldc_Id
				this.form.Ldc_Name = obj.Ldc_Name
				this.form.Refrigeration_Type = obj.Refrigeration_Type
				this.form.Rental_Area = obj.Rental_Area
				this.form.Rental_Location_A = obj.Rental_Location_A
				this.form.Rental_Location_B = obj.Rental_Location_B
				this.form.Rental_Location_C = obj.Rental_Location_C
				this.form.Start_Time = obj.Start_Time
      	this.form.End_Time = obj.End_Time
      	this.form.Is_Active = isActive
      	this.form.Creator = obj.Creator
      	this.form.Created_Time = obj.Created_Time
      	this.form.Updated_Time = DateFtt('yyyy-MM-dd hh:mm:ss', new Date(), false)
      	this.form.changetype = 'updated'
			},
			saveRentStorage (formName) {
				this.$refs[formName].validate((valid) => {
					if (valid) {
						let startTime
						let endTime
						if (typeof (this.form.Start_Time) === 'string') {
							if (this.form.Start_Time === '%' || this.form.Start_Time === '') {
								startTime = null
							} else {
								startTime = this.form.Start_Time
							}
						} else {
							startTime = DateFtt('yyyy-MM-dd', this.form.Start_Time, false)
						}
						if (typeof (this.form.End_Time) === 'string') {
							if (this.form.End_Time === '%' || this.form.End_Time === '') {
								endTime = null
							} else {
								endTime = this.form.End_Time
							}
						} else {
							endTime = DateFtt('yyyy-MM-dd', this.form.End_Time, false)
						}
						let params = [{
							Lease_Id: this.form.Lease_Id,
							Operator_Id: this.form.Operator_Id,
							Con_Id: this.form.Consignor_Id,
							Ldc_Id: this.form.Ldc_Id,
							Refrigeration_Type: this.form.Refrigeration_Type,
							Rental_Area: this.form.Rental_Area,
							Rental_Location_A: this.form.Rental_Location_A,
							Rental_Location_B: this.form.Rental_Location_B,
							Rental_Location_C: this.form.Rental_Location_C,
							Start_Time: startTime,
							End_Time: endTime,
							Is_Active: this.form.Is_Active ? 'Y' : 'N',
							Creator: this.form.Creator,
							Created_Time: this.form.Created_Time,
							Updated_Time: this.form.Updated_Time,
							changetype: this.form.changetype
						}]
						Api.basePost('DS_FD_WAREHOUSE_LEASING', params, true).then((res) => {
							if (res.Flag) {
								this.$alert('保存成功！', '提示', {
            			confirmButtonText: '确定',
            			callback: action => {
            				this.refreshDetail('form')
            				this.$emit('active-name-change', 'list')
            				Bus.$emit(Types.refreshRentStorage)
            			}
            		})
							} else {
								this.$alert(res.ErrInfo, '提示', {
									confirmButtonText: '确定'
								})
							}
						})
					} else {
						return false
					}
				})
			}
		},
		mounted () {}
	}
</script>

<style lang="less">
	.rentStorage {
		.detail {
			.common-condition-box {
				padding: 12px 10px 0 0;
			}
			.common-row {
				.common-col {
					margin-bottom: 12px;
					position: relative;
					overflow: visible;
					.label {
						width: 120px;
					}
					.content {
						overflow: visible;
					}
					.addRule, .deleteRule {
						position: absolute;
						left: 50px;
						margin: 0;
						padding: 5px 12px;
					}
					.addRule {
						top: 35px;
					}
					.deleteRule {
						top: 65px;
					}
				}
			}
			.label.required:before {
				content: "*";
		    color: #ff4949;
		    margin-right: 4px;
			}
			.el-form-item {
				margin: 0;
			}
			.el-form-item__content{
				line-height: 24px;
				width: 100%;
			}
		}
		.condition-item {
			border-bottom: 1px dotted #ccc;
			margin-bottom: 10px;
			padding-bottom: 5px;
			&:nth-last-of-type(1) {
				border: none;
				margin-bottom: 0;
			}
			.el-form-item {
				display: flex;
				.el-form-item__label {
					min-width: 95px;
				}
				.el-form-item__content {
					flex: 1;
				}
			}
		}
		.el-input input.el-input__inner {
			height: 100%!important;
		}
		.el-select__tags {
	    white-space: nowrap;
    	overflow: hidden;
		}
		.el-checkbox {
			margin-right: 10px;
		}
	}
</style>