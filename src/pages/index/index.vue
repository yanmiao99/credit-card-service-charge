<template>
	<view class="wrapper">
		<u--form labelPosition="top" :model="model" :rules="rules" ref="uForm" labelWidth="200rpx">
			<u-form-item label="收款金额 (元) " prop="info.amount" border>
				<u--input clearable v-model="model.info.amount" placeholder="请输入收款金额" prefixIcon="rmb-circle" />
			</u-form-item>
			<u-form-item label="费率 (%)" prop="info.rate" border>
				<u--input clearable v-model="model.info.rate" placeholder="请输入费率" prefixIcon="rmb-circle" />
			</u-form-item>
			<u-form-item label="手续费 (元) " prop="info.handlingFee" border>
				<u--input v-model="model.info.handlingFee" :disabled="true" placeholder="自动计算得出"
					prefixIcon="rmb-circle-fill" />
			</u-form-item>
			<u-form-item label="实际到账 (元) " prop="info.actualAccount" border>
				<u--input v-model="model.info.actualAccount" :disabled="true" placeholder="自动计算得出"
					prefixIcon="rmb-circle-fill" />
			</u-form-item>
			<u-form-item>
				<u-button class="btn" type="primary" text="计 算" @tap="submit" />
			</u-form-item>
		</u--form>
		<!-- 规则 -->
		<view class="computer-rule">
			<text>Tips:</text>
			<view>1、使用前请先检查本页面的计算结果和您的预期是否一致，如果一致请再继续使用，否则此处可能不符合您的实际情况;</view>
			<view>2、计算原理：假如收款10000元，手续费率为0.6，那么实际到帐金额是： 10000-10000*0.6% = 10000-10000*0.6*0.01 = 9940(元);</view>
			<view>
				3、根据现行的《中国银联入网机构银行卡跨行交易收益分配办法》，银行卡收单业务的结算手续费全部由商户承担，但不同行业所实行的费率不同，费率标准从0.5%到4%不等。一般来说，零售业的刷卡手续费率在0.8%-1%，超市是0.5%，餐饮业为2%;
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				model: {
					info: {
						amount: '', // 金额
						rate: '', // 费率
						handlingFee: '', // 手续费
						actualAccount: '', // 实际到账
					}
				},
				rules: {
					'info.amount': {
						type: 'number',
						required: true,
						message: '请填写数字',
						trigger: ['blur', 'change']
					},
					'info.rate': {
						type: 'number',
						required: true,
						message: '请填写数字',
						trigger: ['blur', 'change']
					},
				},
			}
		},
		 /* 用户点击右上角分享 */
		onShareAppMessage: function() {

		},
		/* 分享朋友圈 */
		onShareTimeline: function() {
		
		},
		methods: {
			// 提交事件
			submit() {
				this.$refs.uForm.validate().then(() => {

					// 计算公式
					let res = this.model.info.amount - this.model.info.amount * this.model.info.rate * 0.01

					// 赋值
					this.model.info = {
						...this.model.info,
						handlingFee: (this.model.info.amount - res).toFixed(2),
						actualAccount: res.toFixed(2),
					}

				}).catch(errors => {
					uni.$u.toast('校验失败')
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.wrapper {
		padding: 40rpx;
		box-sizing: border-box;

		.btn {
			margin: 30rpx 0;
		}

		.computer-rule {
			color: $u-tips-color;

			view {
				margin: 20rpx 0;
				font-size: 30rpx;
			}
		}
	}
</style>
