# 工具用量网关的计费责任转移与关账调整

用量为合成整数单位。portion 是供应商用于同一次调用的工作范围标识。correct 明示替换哪个实耗回执。

## 输入消息

每个 JSONL 文件是一段独立输入记录。每行包括整数秒点 at、消息名称 kind 与对象 record。at 代表中间件收到消息的时刻，同一秒内按行序提交。业务时间和对应回执引用写在 record 中。

- **limit**　team/period 的 cap 及 start/end。
- **owner**　任务 task 自 from 秒点起改由 team 承担。
- **call**　任务发起 id、预报 ceiling，供应商按发起许可开始执行。
- **usage**　id 的 receipt、portion、units，finish 指明是否终结，covers 列同一调用累计覆盖的回执标识。 finish 为 true 时 units 是总量；有 covers 指定覆盖的历史回执，未列出的非重叠明确已有实耗继续可追查，若双方覆盖口径冲突则挂起结算。
- **close**　结算员锁定 period 的账单。
- **correct**　更正指定 id 及 receipt 到 units，adjustment 为更正身份。 posting_period 指向已知开放期间。演练期的正差从 posting_period 当前可用额度扣除，原账期留历史对照。
- **restart**　重启。
- **inspect**　按 team 或 id 查询。

## 示例

- handoff-and-late-meter.jsonl
- correction-after-close.jsonl

## 读取演练资料

运行环境为 Python 3.11，只使用标准库。在根目录运行以下命令可核对文件格式及读取接口。

    python3 scenario_reader.py examples
    python3 -m unittest discover -v

资料读取器会按文件统计各类参与方消息的数量。演练记录只描述各参与方交给系统的输入。
