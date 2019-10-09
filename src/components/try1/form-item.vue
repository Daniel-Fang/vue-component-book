<template>
	<div>
		<label v-if="label">{{ label }}</label>
		<slot></slot>
	</div>
</template>

<script>
import AsyncValidator from 'async-validator';
import Emitter from '../../mixins/emitter.js';

export default {
	name: 'iFormItem',
	mixins: [Emitter],
	inject: ['form'],
	props: {
		label: {
			type: String,
			default: ''
		},
		prop: {
			type: String,
		}
	},
	data() {
		return {
			isRequired: false,  // 是否为必填
			validateState: '',  // 校验状态
			validateMessage: '',  // 校验不通过时的提示信息
		};
	},
	mounted() {
		if (this.prop) {
			this.dispatch('iForm', 'on-form-item-add', this);
			this.setRules();
		}
    },
    // 组件销毁前，将实例从 Form 的缓存中移除
    beforeDestroy () {
        this.dispatch('iForm', 'on-form-item-remove', this);
    },
	methods: {
		setRules() {
			let rules = this.getRules();
			if (rules.length) {
				rules.every((rule) => {
					// 如果当前校验规则中有必填项，则标记出来
					this.isRequired = rule.required;
				});
			}
			this.$on('on-form-blur', this.onFieldBlur);
			this.$on('on-form-change', this.onFieldChange);
		},
		getRules() {
			let formRules = this.form.rules;
			formRules = formRules ? formRules[this.prop] : [];
			return [].concat(formRules || []);
		},
		onFieldBlur() {
			this.validate('blur');
		},
		onFieldChange() {
			this.validate('change');
		},
		// 只支持 blur 和 change，所以过滤出符合要求的 rule 规则
		getFilteredRule(trigger) {
			const rules = this.getRules();
			return rules.filter(rule => !rule.trigger || rule.trigger.indexOf(trigger) !== -1);
		},
		validate(trigger, callback = function () { }) {
			let rules = this.getFilteredRule(trigger);

			if (!rules || rules.length === 0) {
				return true;
			}

			// 设置状态为校验中
			this.validateState = 'validating';

			// 以下为 async-validator 库的调用方法
			let descriptor = {};
			descriptor[this.prop] = rules;

			const validator = new AsyncValidator(descriptor);
			let model = {};

			model[this.prop] = this.fieldValue;

			validator.validate(model, { firstFields: true }, errors => {
				this.validateState = !errors ? 'success' : 'error';
				this.validateMessage = errors ? errors[0].message : '';

				callback(this.validateMessage);
			});
		},
	}
}
</script>

<style scoped>
</style>