<template>
	<view>
		<!-- 注意，如果需要兼容微信小程序，最好通过setRules方法设置rules规则 -->
		<u--form
				labelPosition="left"
				:model="model1"
				:rules="rules"
				ref="form1"
		>
			<u-form-item
					label="用户名"
					prop="userInfo.name"
					labelWidth="50"
					borderBottom
					ref="<strong>item1</strong>"
			>
				<u--input
						v-model="model1.userInfo.name"
						border="none"
				></u--input>
			</u-form-item>
			<u-form-item
					label="性别"
					prop="userInfo.sex"
					borderBottom
					@click="showSex = true; hideKeyboard()"
					ref="item1"
			>
				<u--input
						v-model="model1.userInfo.sex"
						disabled
						disabledColor="#ffffff"
						placeholder="请选择性别"
						border="none"
				></u--input>
				<u-icon
						slot="right"
						name="arrow-right"
				></u-icon>
			</u-form-item>
			<u-form-item
					label="密码"
					prop="userInfo.password"
					borderBottom
					ref="<strong>item1</strong>"
			>
				<u--input
						v-model="model1.userInfo.password"
						border="none"
						:type='passtype'
				></u--input>
				    <u-icon :name="showpass ? 'eye-fill' : 'eye'" color="#2979ff" size="28"  slot="right" @click="showp" ></u-icon>
			</u-form-item>
			<u-form-item
					label="手机号"
					prop="userInfo.phone"
					labelWidth="50"
					borderBottom
					ref="<strong>item1</strong>"
			>
				<u--input
						v-model="model1.userInfo.phone"
						border="none"
						maxlength='11'
				></u--input>
				<u-button :text="gettext" slot="right" @click="getcode"></u-button>
			</u-form-item>
			<u-form-item>
				<u-code-input v-model="value" :maxlength="4" style="margin-left: 200rpx;"></u-code-input>
				<view slot="right" class="yzm">
					{{yzm}}
				</view>
			</u-form-item>
		</u--form>
		<u-action-sheet
				:show="showSex"
				:actions="actions"
				title="请选择性别"
				description="如果选择保密会报错"
				@close="showSex = false"
				@select="sexSelect"
		>
		</u-action-sheet>
	</view>
</template>

<script>
export default {
	data() {
		return {
			showSex: false,
			showpass:false,
			passtype:"password",
			gettext:"获取验证码",
			yzm:'',
			value:'',
			model1: {
				userInfo: {
					name: '张三',
					sex: '',
					password:"",
					phone:""
				},
			},
			actions: [{
				name: '男',
				},
				{
					name: '女',
				},
				{
					name: '保密',
				},
			],
			rules: {
				'userInfo.name': {
					type: 'string',
					required: true,
					message: '请填写姓名',
					trigger: ['blur', 'change']
				},
				'userInfo.sex': {
					type: 'string',
					max: 1,
					required: true,
					message: '请选择男或女',
					trigger: ['blur', 'change']
				},
				'userInfo.password': {
					type: 'string',
					required: true,
					pattern: /^[0-9a-zA-Z\\W]*$/g,
					transform(value) {
				return String(value);
			},
					message: '密码必须是英数字带特殊符号的',
					trigger: ['blur', 'change']
				},
				'userInfo.phone': {
					type: 'string',
					len: 11,
					required: true,
					validator: (rule, value, callback) => {
								
									return uni.$u.test.mobile(value);
								},
					message: '手机号码不正确',
					trigger: ['blur', 'change']
				},
			},
			radio: '',
			switchVal: false,
			showp(){
                this.showpass=!this.showpass
				if(this.passtype=='password'){
					this.passtype='text'
				}else{
					this.passtype='password'
				}
			},
		};
	},
	methods: {
		sexSelect(e) {
			this.model1.userInfo.sex = e.name
			this.$refs.form1.validateField('userInfo.sex')
		},
		getcode(){
			let a = 5
			uni.hideKeyboard()
			let phone = this.model1.userInfo.phone
			if(phone==0 && phone !=11){
				console.log('错误');
			}else{
				setTimeout(()=>{
					uni.showToast({
						title:'验证码已发送',
						icon:'none'
					})
					let arr= [1,2,3,4,5,6,7,8,9]
					for(let i = 0;i<4;i++){
						let a = Math.floor(Math.random() * arr.length)
						this.yzm+=arr[a]
					}
				
					
				},1000)
				let time =  setInterval(()=>{
					a--
					this.gettext='重新获取('+  a + ')'
					if(a==0){
						clearInterval(time)
						this.gettext='重新获取'
						this.yzm=''
					}
				},1000)
			}
			
		}
	},
	onReady() {
		//如果需要兼容微信小程序，并且校验规则中含有方法等，只能通过setRules方法设置规则。
    	this.$refs.form1.setRules(this.rules)
    },
};
</script>
<style lang="scss">
	.eye{
		position: absolute;
		left: 90vw;
	}
	.yzm{
		margin-right: 50rpx;
		font-size: 100rpx;
		font-weight: 200;
	}
</style>