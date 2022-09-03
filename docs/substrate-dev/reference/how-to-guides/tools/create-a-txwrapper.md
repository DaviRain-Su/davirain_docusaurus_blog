# 为链创建一个 txwrapper
创建一个txwrapper包将为您的链用户扩展离线签名选项。这对于需要使用气隙设备促进交易签名、构建和/或解码的有安全意识的用户来说很重要。这包括（但不限于）托管人、交易所和冷库用户。

在txwrapper为您自己的链构建之前，请查看txwrapper-examples.

确保您了解 Polkadot 示例并了解txwrapper-core最终用户应该使用的方法（请参阅decode、construct.{signingPayload, signedTx, txHash}）。您的包将重新导出这些，因此请务必了解您将创建的公共 API。

目标
构建链txwrapper包的公共 API。

用例
让现有txwrapper用户轻松集成新的 txwrappers。

脚步
1.使用创建一个repotxwrapper-template
将txwrapper-template目录复制到您的工作存储库中。

该模板提供了几乎可以发布到NPM. 导出显示了一些与基于 FRAME 的链相关的方法，这些方法至少使用balances、proxy和utility pallets。

请注意，txwrapper-core\在顶层重新导出以使用户可以访问其工具。

2.更新package.json
修改以下字段以反映您的连锁店信息：

姓名
作者
描述
存储库
错误
主页
私人（标记为假）
此外，添加以下字段以授予发布权限：
```
  "publishConfig": {
    "access": "public"
  },
```
3.选择相关方式复出口
您需要选择要txwrapper公开的托盘方法。建议选择可能被离线存储的密钥签名的方法。

如果您只需要 Substrate 或 ORML 托盘中的方法，请检查txwrapper-substrate和txwrapper-orml以查看方法是否已定义。

4.创建一个getRegistry方法
您的 txwrapper 将需要导出一个getRegistry方法，以便用户可以获得具有TypeRegistry您链的最新类型的 Polkadot-js。

通过一些小的修改，foo下面的示例可以应用于任何FRAME与 Polkadot-js 类型兼容的基于链的链：
```
// src/index.ts

import { typesBundleForPolkadot } from '@foo-network/type-definitions';
import { OverrideBundleType } from '@polkadot/types/types';
import {
  getRegistryBase,
  GetRegistryOptsCore,
  getSpecTypes,
  TypeRegistry,
} from '@substrate/txwrapper-core';

// As a convenience to users we can provide them with hardcoded chain properties
// as these rarely change.
/**
 * `ChainProperties` for networks that txwrapper-foo supports. These are normally returned
 * by `system_properties` call, but since they don't change much, it's pretty safe to hardcode them.
 */
const KNOWN_CHAIN_PROPERTIES = {
  foo: {
    ss58Format: 3,
    tokenDecimals: 18,
    tokenSymbol: 'FOO',
  },
  bar: {
    ss58Format: 42,
    tokenDecimals: 18,
    tokenSymbol: 'FOO',
  },
};

// We override the `specName` property of `GetRegistryOptsCore` in order to get narrower type specificity,
// hopefully creating a better experience for users.
/**
 * Options for the `getRegistry` function.
 */
export interface GetRegistryOpts extends GetRegistryOptsCore {
  specName: keyof typeof KNOWN_CHAIN_PROPERTIES;
}

/**
 * Get a type registry for networks that txwrapper-foo supports.
 *
 * @param GetRegistryOptions specName, chainName, specVersion, and metadataRpc of the current runtime
 */
export function getRegistry({
  specName,
  chainName,
  specVersion,
  metadataRpc,
  properties,
}: GetRegistryOpts): TypeRegistry {
  const registry = new TypeRegistry();
  registry.setKnownTypes({
    // If your types are not packaged in the `OverrideBundleType` format, you can
    // specify types in any of the formats supported by `RegisteredTypes`:
    // https://github.com/polkadot-js/api/blob/4ff9b51af2c49294c676cc80abc6476565c70b11/packages/types/src/types/registry.ts#L59
    typesBundle: (typesBundleForPolkadot as unknown) as OverrideBundleType,
  });

  return getRegistryBase({
    chainProperties: properties || KNOWN_CHAIN_PROPERTIES[specName],
    specTypes: getSpecTypes(registry, chainName, specName, specVersion),
    metadataRpc,
  });
}
```
并添加相关出口：
```
// src/methods/currencies/index.ts

// export the method, effectively making available under the `currencies` namespace
export * from "./transfer";
// src/methods

// Export everything from within `methods`, including the `currencies` namespace, making it so we can
// access the method via `methods.currencies.transfer`
export * as methods from "./methods";
```
5. 创建一个工作示例
一个很好的例子可以减轻用户的摩擦并减少维护者的工作量。创建一个端到端的示例，以便用户清楚地了解为您的链生成离线交易的完整流程。

重命名template-example.ts为适合您的链的名称并更新文件中标记为 TODO 的所有部分。
更新examples/README.md标记为 TODO 的部分。
确保您可以使用链的开发节点运行示例。
6. 发布你的包
一旦确定版本控制有意义并且包在本地工作，请参阅本指南以了解如何将包发布到NPM.

例子
模板示例
资源
如何使用tx-wrapper-polkadot
序列化/反序列化单元测试使用jest