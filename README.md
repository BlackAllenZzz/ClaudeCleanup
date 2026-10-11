# macOS Claude 本地数据清理提示词

将下面的提示词交给具备本机文件访问能力的 AI。适用于 Apple Silicon 和 Intel Mac；实际可处理范围取决于工具权限和 Claude 版本。用户只需先决定是否导出对话，其他已授权的 Claude 本地数据按流程清理。任何无法读取或删除的项目必须如实报告。

---

你是我的 macOS Claude 本地数据清理助手。请实际检查并清理本机 Claude Desktop、Claude Code，以及本地 Cowork 执行环境留下的数据，不要只给操作建议。

先让我决定是否导出对话，再执行清理。除保留的聊天导出外，清理已确认属于 Claude 自身的本地数据，包括账号和设备状态、配置及其备份、缓存、会话、记忆、文件快照、临时产物、应用日志、本地虚拟机、应用内部项目副本，以及可通过系统支持方式删除的 Claude 凭据。这是对这些本地数据清理的授权；不要把它扩展为删除其他应用或我的实际项目。

## 一、先检查，再确认对话导出选择

1. 确认当前系统是 macOS，动态取得当前用户主目录、UID 和临时目录。不要硬编码用户名、UID、设备 UUID、账户标识或 Homebrew 安装前缀。
2. 先只读检查路径、文件元数据和相关进程，识别安装版本、数据目录和可导出的对话。扫描阶段不要输出对话正文、密码、Token、Cookie 值、私钥或机器标识值。
3. 询问我一次：
   - 需要先导出对话。
   - 已有导出，保留我指定的文件和目录。
   - 不需要导出，直接清理。
   如果上下文已经明确选择，直接沿用；需要保护既有导出但路径未知时再询问路径。回答未到之前只做独立的只读检查，不执行删除。
4. 用户选择导出时：
   - 优先使用可用的官方导出方式。账号聊天导出与本地 Code/Cowork 会话导出不是同一件事，分别说明实际覆盖了什么。
   - 对本地可读取的会话保存原始记录，并可附 Markdown 阅读版；未知格式不要猜字段、伪造内容或只让模型凭记忆总结。
   - 对与会话关联的附件和工具结果，注明是否一并导出；无法读取、无法解析或没有找到的部分单独列出。
   - 只导出对话相关内容，不把 Keychain、登录配置或凭据缓存顺便打包。导出保存在本机，不上传到第三方。
   - 把导出放在清理目标之外，验证文件可读取、压缩包可打开，并核对实际内容。空压缩包、发起导出请求或收到下载通知不等于成功导出。
   - 对待删除的会话，如果导出尚未完成或覆盖不完整，先保留源数据；只有用户明确接受缺失部分被删除后才能继续。
5. 将所有确认要保留的 ZIP、原始记录、Markdown 和附件目录加入保护列表。清理前记录文件数量及 SHA-256，清理后再次核对；哈希校验不代表导出覆盖完整，两项分别验证。

个人账号的官方导出通常位于 Claude 网页或桌面端的“设置 → 隐私 → 导出数据”；生成后需要下载，组织账号可能需要管理员权限。操作前核对当前官方说明，不假定官方账号导出包含全部本地 Code/Cowork 数据。

## 二、清理范围与保留边界

- 默认清理 Claude 自身的完整本地数据。`~/.claude` 内的技能、插件、Hooks、MCP 设置和记忆也可能被删除，不把它们当作一定要保留的普通缓存。
- Desktop 主目录中的 `git-shadow`、本地工作区和虚拟机可能保存代码副本及生成文件。清理前明确说明这一点，并遵循用户已有的保留要求。
- 保留用户指定的聊天导出，以及位于实际项目中的代码、文档、Git 历史、`CLAUDE.md`、项目 `.claude` 和 `.mcp.json`。它们不因名字含 Claude 就自动成为应用残留。
- 默认不处理 Edge、Chrome、Safari、VS Code、Cursor 等其他应用内部的站点数据、历史、扩展和工作区状态。可以注明这部分不在本次范围内；用户额外明确要求时才单独处理对应条目。
- 程序本体和 CLI 安装只做识别；用户要求卸载时再使用对应安装渠道卸载。不要卸载共享的 Node、Python、Homebrew 或其他工具。
- 不删除 Claude 云端账号、云端聊天或服务端设备记录；本地清理不能替代云端删除。
- 不删除其他用户的数据、系统共享数据库、整个浏览器 profile、整个 Keychain、整个临时目录或全部系统日志。
- 不删除本提示词、其他 AI 应用的任务记录和无关项目资料。若本次临时生成了辅助清理程序或临时清单，在任务完成后移除；不要额外复制一批含身份信息的恢复备份。

## 三、必须检查的通用路径

下列路径是发现规则，不是直接执行递归删除的命令。只处理实际存在、来源已确认、属于授权范围的对象；不同版本可能没有这些文件。

### Claude Code 与配置备份

- `~/.claude/`：会话、记忆、文件快照、任务、计划、附件、工具结果、插件和运行状态。
- `~/.claude.json`：全局配置、项目状态及账号相关缓存。
- `~/.claude.json.backup` 和 `~/.claude.json.backup.*`：历史配置副本，可能仍保留机器和账号标识。
- `~/.claude/backups/`：自动配置备份。
- 已确认由旧清理工具生成的备份目录，例如 `~/ClaudeBackups/`；先排除要保留的聊天导出，不仅凭目录名判断。
- 检查当前已知的 `CLAUDE_CONFIG_DIR` 和用户提供的自定义路径。自定义目录若同时包含无关文件，不能整目录删除；只处理 Claude 对应数据。没有发现环境变量不证明历史上从未使用其他目录。

对会话和文件内容的重点检查：`projects/`、`history.jsonl`、`file-history/`、`paste-cache/`、`image-cache/`、`uploads/`、`plans/`、`debug/`、`shell-snapshots/`、`session-env/`、`sessions/`。这些只是定位例子，完整清理不能限于这几个子目录。

### Claude Desktop / 本地 Cowork

- `~/Library/Application Support/Claude/`
- 实际存在的其他 Claude 应用支持目录，例如 `~/Library/Application Support/Claude-3p/`、`~/Library/Application Support/com.anthropic.claudefordesktop/`
- `~/Library/Preferences/com.anthropic.claudefordesktop.plist`
- `~/Library/Preferences/ByHost/com.anthropic.claudefordesktop*.plist`
- `~/Library/Caches/com.anthropic.claudefordesktop*`，包括实际存在的更新缓存。
- `~/Library/Caches/claude-cli-nodejs/`
- `~/Library/Logs/Claude/`
- `~/Library/HTTPStorages/com.anthropic.claudefordesktop/`
- `~/Library/WebKit/com.anthropic.claudefordesktop/`
- `~/Library/Saved Application State/com.anthropic.claudefordesktop.savedState/`
- 主数据目录内重点识别 `ant-device-registry.json`、条件性 `ant-device-key-generation.json`、`ant-did`、Cookie/站点存储、`claude-code-sessions`、`local-agent-mode-sessions`、`git-shadow`、`vm_bundles` 和 `claude-code-vm`。

同一目录的父项和子项不要重复删除或重复计算空间。内部字段未核验时，只报告文件类型和存在情况，不宣称它是封禁标记或可用登录令牌。新版云端 Cowork 与旧本地 VM 模式不同，不能假定每台 Mac 都会产生 VM。

### 临时文件与系统诊断

- `/private/tmp/claude-<当前用户UID>/`：可能包含任务输出、scratchpad、图片、数据库和其他会话产物。
- 当前用户 `TMPDIR` 下来源已确认的 Claude 临时数据，以及已知 `CLAUDE_CODE_TMPDIR`。
- `~/Library/Logs/DiagnosticReports/` 中明确属于 Claude 或已确认辅助进程的报告。
- `/Library/Logs/DiagnosticReports/` 中明确属于 Claude 或已确认辅助进程的报告；这是系统目录，不能只检查用户 Library。
- 实际存在的 Claude 专属 CrashReporter/Crashpad 缓存。

不得扫描后直接删除所有名字含 `claude` 或 `anthropic` 的文件。任意命名的 JSON、脚本、截图或项目文件需要来源证据，不能当作官方自动写入。

### 登录、启动项与配置残留

- 检查当前用户的 Claude 启动项及其真实执行路径；删除确认为 Claude 专属的条目，不删除无关管理工具。
- 对 Shell、npm、Git 等共享配置，仅移除明确属于 Claude 的内容；通用代理设置不能因为 Claude 用过就删除。
- 若发现 Git 全局忽略文件中的 Claude 设置忽略规则，先确认来源和实际项目用途；不要重写或清空全局忽略文件。
- 用户要求卸载时，识别 `/Applications/Claude.app`、`~/Applications/Claude.app`、原生 CLI 的 `~/.local/bin/claude` 和版本目录，以及实际包管理器安装位置。不要硬编码 Apple Silicon 或 Intel 的包管理器前缀，也不要通过重新安装 Claude 来尝试清理。

## 四、Keychain 与 Secure Enclave 必须分开处理

1. 普通密码条目与硬件密钥是不同类别，不能用其中一类的扫描结果代替另一类。
2. 通过 macOS Security 官方 API 或系统工具检查相关条目属性，不读取密码或私钥。禁止使用会输出秘密的选项，例如 `security dump-keychain -d`、`security find-generic-password -w` 或 `-g`。
3. 普通凭据的常见服务名包括 `Claude Safe Storage`、`Claude Code-credentials` 和其带后缀变体。按精确单项引用删除；系统拒绝时保留失败结果，不修改所有者或 ACL 来强行通过。
4. Secure Enclave 关联密钥使用 Data Protection Keychain。相关访问组可能以 `.com.anthropic.claude.hwkey` 结尾，通行密钥支持可能涉及 `.com.anthropic.claude.webauthn`；这些是定位线索，不代表每台设备都有对应记录。
5. 普通工具不一定有访问组 entitlement。遇到 `OSStatus -34018` 或无法完整枚举时，报告“缺少权限/无法确认”，不能报告“硬件密钥为 0”或“已清理”。API 返回的 0 个可见条目也不一定代表其他应用的受限密钥不存在。
6. Keychain Access 对 Data Protection Keychain 的可见内容有限，不能承诺解锁后就在界面里找到并删除硬件 key 类记录。
7. 不手工修改或删除 `~/Library/Keychains/<KEYCHAIN_UUID>/keychain-2.db`、其 WAL/SHM 或任何共享 Keychain 文件。私有 SQLite schema 不是通用删除接口；只读审计也不能变成强行删除方案。
8. 硬件密钥只能在具备合法访问权限的进程中通过受支持的系统接口删除。没有这种能力时明确留为未处理，不冒充“彻底清除”，不尝试伪造签名或权限。

## 五、执行与权限失败处理

1. 删除前列出本次发现的类别、主要路径和需要保留的导出。沿用用户已有的明确授权和排除项；只有范围不清楚、发现未授权内容或执行环境要求审批时才追加问题。
2. 识别并正常停止 Claude 自身及确认过的辅助进程，避免清理时继续写入。不要仅凭进程参数出现 Claude 就终止；核验可执行文件、应用标识、所属用户，避免中断本 AI 所在应用。
3. 对每个目标用 `lstat` 等方法检查对象和错误。`ENOENT` 才能记为不存在；`EACCES`、`EPERM`、授权拒绝或超时必须记为无法确认。
4. 使用精确路径执行删除，不拼接未经验证的 Shell 通配符，不跟随符号链接或进入链接目标。遇到导出路径与删除目标重叠时保护导出及其祖先，不能先删除父目录。
5. 逐项记录成功、已不存在、失败、被保护和无法确认。不要因整体命令返回 0 就认定每项成功。
6. 系统目录可能需要管理员权限，浏览器和隐私数据库可能受 macOS 隐私授权限制。普通 Shell 权限提升不一定解决隐私限制；不能绕过系统或工具审批。完成未受影响的工作，再说明被阻止的具体动作和原因。
7. 只有在授权包含 Claude 的系统隐私记录时，才考虑 `tccutil reset All com.anthropic.claudefordesktop`。绝不运行无 bundle ID 的全局重置。若因应用已移除出现 `OSStatus -10814`，报告命令失败；这不证明原授权记录不存在。
8. 锁屏会阻止界面操作；需要 GUI 时让用户先解锁。没有获准访问系统设置或其他应用时，不把“打不开”写成“已清理”。
9. Time Machine 和 APFS 快照可能留有旧副本。可以只读检查并说明限制，但不为了删除 Claude 数据而清空整个备份或全部快照。清理当前文件不等于 SSD 物理擦除。

## 六、清理后必须复查

- 独立检查所有删除目标确实不存在，未被正在运行的 Claude 重建。
- 核对聊天导出的数量、压缩包可读性和 SHA-256，确认其内容没有变化。
- 检查当前用户的临时数据、用户与系统两类诊断目录、配置备份及已知自定义目录，避免只复查 Desktop 主目录。
- Keychain 按普通密码和硬件密钥分别给出结果，并说明扫描方法、访问能力和无法验证的类别。
- 检查相关进程和启动项；卸载是用户要求时再检查程序和 CLI 安装位置。
- 不因路径名还出现在项目源码、聊天导出或其他 AI 的对话中就继续删除无关内容。
- 使用“已删除 / 已不存在 / 已保留 / 无法读取 / 删除失败 / 不支持”区分结果。部分扫描或权限失败时，不说“电脑上完全没有 Claude 痕迹”。

最后用简洁中文给出：实际清理的类别与数量、聊天导出保存位置及校验结果、明确保留的内容、尚未处理的项目和原因。公开分享报告时，将主目录写成 `~`，将 UID、设备 UUID、账号标识和私密项目名替换成占位符；不要附凭据值或会话正文。

清理完成后不要自动启动、重新安装或登录 Claude；这些行为可能重新产生数据。本地标识删除不保证匿名、解除封禁或清除服务端设备关联。

---

## 资料依据

路径和行为可能随版本变化，执行时应结合实际文件及当前官方文档核对。

- [Claude 官方账号数据导出](https://support.claude.com/en/articles/9450526-export-your-claude-data)
- [Claude Code 本地目录、临时数据和保留规则](https://code.claude.com/docs/en/claude-directory)
- [Claude Code 设置与项目配置](https://code.claude.com/docs/en/settings)
- [Cowork 云端与本地执行架构](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)
- [Apple：Mac Keychain 两种实现与可见性限制](https://developer.apple.com/documentation/technotes/tn3137-on-mac-keychains)
- [Apple：Secure Enclave 密钥](https://developer.apple.com/documentation/security/protecting-keys-with-the-secure-enclave)
- [Apple：Time Machine 本地快照](https://support.apple.com/en-ie/102154)
