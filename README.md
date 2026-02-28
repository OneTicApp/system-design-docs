# Africa FinTech 系统分析文档

> 深度分析非洲金融科技系统的架构、模块与业务流程

## 📁 文档结构

```
analysis/
├── README.md                         # 本文件 - 文档入口
├── user_flow_diagrams.html           # 用户流程图
├── dependency/                       # 外部依赖
│   └── EXTERNAL_DEPENDENCIES.md      # 依赖说明
├── modules/                          # 模块深度分析
│   ├── module_login_analysis.md      # 登录模块
│   ├── module_kyc_analysis.md        # KYC模块
│   ├── module_credit_analysis.md     # 信贷模块
│   └── module_savings_analysis.md    # 储蓄模块
└── design/                           # 系统设计文档
    └── subsystems/                   # 子系统设计文档
        ├── FTISP-AAC-系统设计文档.md  # 统一接入中心
        ├── FTISP-UAM-系统设计文档.md  # 用户账号管理
        ├── FTISP-DAL-系统设计文档.md  # 数据访问层
        └── FTISP-ECIF-系统设计文档.md # ECIF中心
```

## 📦 模块分析

| 模块 | 说明 | 文档 |
|------|------|------|
| 登录/认证 | 用户登录、会话管理 | [module_login_analysis.md](modules/module_login_analysis.md) |
| KYC | 用户注册、身份验证 | [module_kyc_analysis.md](modules/module_kyc_analysis.md) |
| 信贷 | 贷款申请、审批、还款 | [module_credit_analysis.md](modules/module_credit_analysis.md) |
| 储蓄 | 存款账户、利息计算 | [module_savings_analysis.md](modules/module_savings_analysis.md) |

## 🚀 快速开始

推荐阅读顺序：
1. [user_flow_diagrams.html](user_flow_diagrams.html) - 可视化流程
2. 各模块深度分析（按需查阅）

## 📌 核心子系统

| 子系统 | 职责 |
|--------|------|
| FTISP-AAC | 统一入口、路由分发 |
| FTISP-UAM | 用户认证、资料管理 |
| FTISP-DAL | 业务编排中心 |
| FTISP-KYC | OCR、NIDA核查、活体检测 |
| FTISP-MSG | 短信通知 |
| FTISP-ECIF | ECIF中心 |
| FTISP-ACS | 账户核心系统 |

---

**分析日期**: 2026年2月
**更新频率**: 按需更新
