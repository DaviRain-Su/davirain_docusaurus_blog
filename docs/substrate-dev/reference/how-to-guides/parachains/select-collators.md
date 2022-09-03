# 选择整理者
本指南对于准备在生产中启动平行链、探索整理者选择的不同选项的团队很有用。

对于平行链网络的收集人来说，重要的是要确保存在一些中立的收集人来防止审查——但不一定是多数人。避免拥有过多的整理者也很重要，因为它们可能会减慢网络速度。本指南逐步介绍了设计平行链网络时要考虑的因素。

整理者选择
您可以自由选择整理者选择方法。常见的方法包括股权投票或通过委员会或其他来源（如民主）直接分配整理者。在这两种情况下，创建一个托盘来实现最适合您需求的逻辑。

股权投票
Cumuluscollator-selection托盘是实施权益投票以选择收集人的一个实际示例。

使用链上治理
实现一个特殊的来源，允许该来源的成员成为整理者。使用民主托盘来选举这些成员并在专用于处理整理者选择的托盘中定义它们：

    /// Configuration trait of this pallet.
	#[pallet::config]
	pub trait Config: frame_system::Config {
        // --snip-- //
        type MySpecialOrigin: EnsureOrigin<Self::Origin>;
    }
    // --snip-- //
    #[pallet::call]
	impl<T: Config> Pallet<T> {
		/// Some set-collator dispatchable.
		#[pallet::weight(some_weight)]
		pub fn set_collator( origin: OriginFor<T>) -> DispatchResultWithPostInfo {
            T::MySpecialOrigin::ensure_origin(origin)?;
            // --snip-- //
        }

也有不同的方式来实施对整理者的激励。看看这个例子来探索如何。

例子
使用交易费用激励收集人选择的累积实施。
Moonbeam 使用通胀货币政策抵押方案实施收集人选择。
资源
平行链 DevOps 最佳实践
