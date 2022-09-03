# 计算费用

这将引导您逐步完成WeightToFee为运行时的pallet_transaction_payment. 费用分为三个部分：

字节费- 与交易的字节长度成比例的费用。比例常数是交易支付托盘中的一个参数。
重量费- 根据交易重量计算的费用。转换不需要是线性的，尽管它经常是。相同的转换函数适用于运行时中所有托盘的所有事务。
费用乘数- 计算费用的乘数，可以随着链的进展而变化。
FRAME 提供交易支付托盘，用于计算和收取执行交易的费用。修改费用的计算方式以更准确地收取费用可能很有用。

目标
自定义WeightToFee以修改为您的运行时计算费用的方式。

用例
修改费用的计算方式，而不是使用IdentityFee将一个单位的费用映射到一个单位的重量。

脚步
1. 编写LinearWeightToFee结构
在runtime/src/lib.rs中，创建名为LinearWeightToFeeimplements的结构WeightToFeePolynomial。它必须返回一个 smallvec 的WeightToFeeCoefficient整数。

`runtime/src/lib.rs`
```
pub struct LinearWeightToFee<C>(sp_std::marker::PhantomData<C>);

impl<C> WeightToFeePolynomial for LinearWeightToFee<C>
where
	C: Get<Balance>,
{
	type Balance = Balance;

	fn polynomial() -> WeightToFeeCoefficients<Self::Balance> {
		let coefficient = WeightToFeeCoefficient {
			coeff_integer: C::get(),
			coeff_frac: Perbill::zero(),
			negative: false,
			degree: 1,
		};

		smallvec!(coefficient)
	}
}
```
2.pallet_transaction_payment在你的运行时配置
将派送重量转换 type WeightToFee为收费费用LinearWeightToFee（替换`IdentityFee<Balance>;`）：

`runtime/src/lib.rs`
```
parameter_types! {
    // Used with LinearWeightToFee conversion.
	pub const FeeWeightRatio: u128 = 1_000;
	// Establish the byte-fee. It is used in all configurations.
	pub const TransactionByteFee: u128 = 1;
}

impl pallet_transaction_payment::Config for Runtime {
	type OnChargeTransaction = CurrencyAdapter<Balances, ()>;
	type TransactionByteFee = TransactionByteFee;

	// Convert dispatch weight to a chargeable fee.
	type WeightToFee = LinearWeightToFee<FeeWeightRatio>;

	type FeeMultiplierUpdate = ();
}
```
相关资料
重量
添加基准
使用自定义权重
交易权重和费用
WeightToFeeCoefficients
WeightToFeePolynomial