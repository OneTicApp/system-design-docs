# FTISP-DAL 外部接口依赖清单

**对接方**: DAL/AAC/UAM 团队
**日期**: 2026-02-15
**状态**: 待对接

为了支持 **移动支付入金 (Mobile Money Deposit)** 和 **定期存款 (Term Deposits)** 业务，FTISP-DAL (编排层) 需要以下下游系统提供相应的 API 支持。

---

## 1. 交易系统 (FTISP-CTS) - 🔴 优先级：最高 (Blocker)
> **说明**: 目前 CTS 模块缺失，无法进行任何入金和定期存款业务。需要 CTS 团队尽快提供以下接口。

| 接口名称 | HTTP 方法 | 接口路径 | 用途 | 备注 |
| :--- | :--- | :--- | :--- | :--- |
| **发起入金** | `POST` | `/api/v1/internal/trade/deposits` | 处理移动支付入金请求，对接网关 | DAL透传AAC请求 |
| **获取渠道配置** | `GET` | `/api/v1/internal/config/channels` | 获取 M-Pesa/Tigo/Airtel 渠道列表 | App端展示需要 |
| **定期存款预览** | `POST` | `/api/v1/internal/trade/savings/preview` | 购买前试算(预期收益) | 返回预期利息和到期金额 |
| **创建定期存款** | `POST` | `/api/v1/deposit/fixed` | 购买定期存款 (对接银行核心) | 需传入 `ecif_no`, `product_id` |
| **定期存款预览** | `POST` | `/api/v1/internal/trade/savings/preview` | 购买前试算(预期收益) | 返回预期利息和到期金额 |
| **创建定期存款** | `POST` | `/api/v1/deposit/fixed` | 购买定期存款 (对接银行核心) | 需传入 `ecif_no`, `product_id` |
| **创建保证金定期存款** | `POST` | `/api/v1/deposit/security-deposit` | **Security Deposit专用** | `isSecurityDeposit=true`, 强制AUTO_ROLLOVER |
| **赎回预览(算费)** | `POST` | `/api/v1/internal/trade/savings/preview-redemption` | 赎回试算(含罚息计算) | 提前赎回: 50%利息罚息 |
| **赎回定期存款** | `POST` | `/api/v1/internal/trade/savings/redeem` | 提前赎回定期存款 | 需传入 `ecif_no`, `deposit_id` |
| **转账预览** | `POST` | `/api/v1/internal/trade/transfers/preview` | 计算转账手续费 | 返回 totalAmount + fee |
| **执行转账** | `POST` | `/api/v1/internal/trade/transfers/execute` | 执行转账交易 | 需支持 X-Pin-Token 鉴权 |
| **收款人反查** | `GET` | `/api/v1/internal/trade/transfers/recipient-name` | 内部转账反查户名 | 返回掩码后的姓名 |
| **取现预览** | `POST` | `/api/v1/internal/trade/withdrawals/preview` | 计算取现手续费 | 返回 totalAmount + fee |
| **生成取现码** | `POST` | `/api/v1/internal/trade/withdrawals/generate-code` | 生成 Wakala 取现码 | 需支持 X-Pin-Token 鉴权 |
| **Wakala核销回调** | `POST` | `/api/v1/callbacks/wakala/redeem` | 代理商核销取现码 | 外部回调接口 |
| **查看存款详情** | `GET` | `/api/v1/internal/cts/savings/{depositId}` | 查询定期存款详情 | 返回完整存款信息，含canModifyInstruction |
| **修改到期指令** | `PUT` | `/api/v1/internal/cts/savings/{depositId}/instruction` | 修改到期指令 | 支持AUTO_ROLLOVER ↔ CREDIT_TO_ACCOUNT |
| **查询定期存款列表** | `GET` | `/api/v1/internal/cts/savings` | 查询用户定期存款列表 | 支持status过滤 (ACTIVE/MATURED/REDEEMED) |

## 2. 客户中心 (FTISP-ECIF) - 🔴 优先级：高
> **说明**: DAL 需要通过 ECIF 获取客户基础信息 (如 ECIF 号)，用于调用下游 CTS 等系统。

| 接口名称 | HTTP 方法 | 接口路径 | 用途 | 备注 |
| :--- | :--- | :--- | :--- | :--- |
| **查询客户信息** | `GET` | `/api/v1/internal/ecif/customers/uid/{userId}` | 根据 UserID 获取客户档案 (含 ecif_no) | 核心基础服务 |

## 3. 账户核心 (FTISP-ACS) - 🟠 优先级：高
> **说明**: 需要 ACS 提供资金冻结能力，以支持定期存款和交易预授权。

| 接口名称 | HTTP 方法 | 接口路径 | 用途 | 备注 |
| :--- | :--- | :--- | :--- | :--- |
| **冻结余额** | `POST` | `/api/v1/internal/acs/balance/freeze` | 冻结指定金额（如定期存款本金） | 需返回冻结流水号 |
| **解冻余额** | `POST` | `/api/v1/internal/acs/balance/unfreeze` | 解冻资金（如定期到期） | |
| **核心记账** | `POST` | `/api/v1/internal/acs/posting/entry` | 执行资金划转（入金/出金） | 需支持幂等性 |

## 3. 消息中心 (FTISP-MSG) - 🟡 优先级：中
> **说明**: 需要补充具体的业务通知模板，确保用户能收到正确的短信/推送。

| 资源名称 | 类型 | 标识 (Code) | 用途 | 内容示例 |
| :--- | :--- | :--- | :--- | :--- |
| **充值成功模板** | Template | `DEPOSIT_SUCCESS` | 入金成功通知 | "您充值的 {amount} 已到账。" |
| **充值失败模板** | Template | `DEPOSIT_FAILED` | 入金失败通知 | "充值失败，原因：{reason}。" |
| **定期到期模板** | Template | `SAVINGS_MATURITY` | 定期存款到期通知 | "您的定期存款已到期。" |

## 4. 授信系统 (FTISP-DRE) - 🔴 优先级：最高 (Blocker)
> **说明**: Security Deposit 模块核心依赖，负责管理用户授信额度。需要提供额度增加/减少接口。

| 接口名称 | HTTP 方法 | 接口路径 | 用途 | 备注 |
| :--- | :--- | :--- | :--- | :--- |
| **增加授信额度** | `POST` | `/api/v1/internal/credit/limit/increase` | **Security Deposit提额** | 增加CB额度，更新用户类型 |
| **减少授信额度** | `POST` | `/api/v1/internal/credit/limit/decrease` | **保证金赎回减额** | 减少CB额度，降级用户类型 |
| **查询授信额度** | `GET` | `/api/v1/internal/credit/limit/{userId}` | 查询用户当前授信额度 | 返回creditLimit |
| **查询活跃贷款** | `GET` | `/api/v1/internal/credit/loans/active` | **保证金赎回前检查** | 检查是否有未结清贷款 |
| **更新用户类型** | `PUT` | `/api/v1/internal/credit/user-type` | 更新用户类型 | TYPE_C ↔ TYPE_C_WITH_CREDIT |

## 5. 风控系统 (FTISP-RCS) - 🟡 优先级：中
> **说明**: 入金流程需要经过风控扫描。

| 接口名称 | HTTP 方法 | 接口路径 | 用途 | 备注 |
| :--- | :--- | :--- | :--- | :--- |
| **交易风控扫描** | `POST` | `/api/v1/internal/risk/scan` | 实时评估交易风险 | 返回 PASS/REJECT/REVIEW |
