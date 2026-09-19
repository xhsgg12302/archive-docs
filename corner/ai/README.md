---
tocEndLevel: 5
cascade : 
    tags: ["sence", "demo", "article", "ai"]
---

* ## Intro(AI | Artificial Intelligence)

    + ### 概念解释

        - #### LLM(Large Language Model)

            LLM 是大语言模型（Large Language Model）的英文缩写，是一种基于海量文本数据训练的人工智能系统，能够理解、总结、推理和生成类人语言。 [1](https://botpress.com/tw/blog/best-large-language-models), [2](https://botpress.com/zh-cn/blog/best-large-language-models)

            本质：一种拥有庞大参数量的深度学习模型，利用神经网络（主要是 Transformer 架构）来预测和生成下一个词。
            <br>能力：它不只擅长单一任务，还能同时处理文字创作、翻译、编程写代码、问答和数据分析等多项工作。

            目前主流的 LLM 有:
            <br>闭源：OpenAI GPT 系列：如 `GPT-4`、`GPT-4o`，`Google Gemini`，Anthropic Claude 系列：如 `Claude 3.5`
            <br>开源：Meta Llama 系列：如 `Llama 3`，`DeepSeek`，`Qwen`，智谱 AI 的`GLM`等。

        - #### RAG(Retrieval-Augmented Generation)

            RAG（检索增强生成，英文全称为 Retrieval-Augmented Generation）是一种结合了“外部知识检索”与“大模型文本生成”的 AI 技术架构。 [1](https://zhuanlan.zhihu.com/p/675509396), [2](https://www.systexdc.com/2025/11/12/rag-technology/), [3](https://blog.csdn.net/kevinjin2011/article/details/142253724)简单来说，它就像是给大语言模型（如 ChatGPT）外挂了一个专属的资料图书馆和搜索引擎。 [1](https://cloud.google.com/use-cases/retrieval-augmented-generation?hl=zh-CN), [2](https://www.systexdc.com/2025/11/12/rag-technology/)

            为什么需要 RAG？
            <br>传统的大模型虽然聪明，但有三个明显的缺点： [1](https://www.redhat.com/zh-cn/topics/ai/what-is-retrieval-augmented-generation), [2](https://blog.csdn.net/kevinjin2011/article/details/142253724)
            <br>知识过时：它们的知识截止于训练完成的那一刻，无法自动获取最新信息。
            <br>瞎编乱造（幻觉）：当遇到不知道或不确定的问题时，它不会承认，而是会一本正经地胡说八道。
            <br>缺乏私密数据：它没读过你公司的内部文件、个人的私有文档或最新的专业资料。 [1](https://www.intel.com.tw/content/www/tw/zh/learn/what-is-rag.html), [2](https://www.redhat.com/zh-cn/topics/ai/what-is-retrieval-augmented-generation), [3](https://blog.csdn.net/kevinjin2011/article/details/142253724)
            <br>如果选择对大模型进行“重新训练或微调（Fine-tuning）”，成本极其高昂且耗时。而 RAG 是一种花钱少、见效快的替代方案。

            RAG 是怎么工作的？
            <br>RAG 的工作流程通常分为两个核心阶段： [1](https://zhuanlan.zhihu.com/p/675509396), [2](https://www.redhat.com/zh-cn/topics/ai/what-is-retrieval-augmented-generation)
            <br>准备阶段（离线）：把你的私域文档（如公司规章、PDF、产品手册等）切成小段。把这些小段文字转化成计算机能理解的向量（Embedding），存入向量数据库中。 [1](https://zhuanlan.zhihu.com/p/675509396), [2](https://www.redhat.com/zh-cn/topics/ai/what-is-retrieval-augmented-generation)
            <br>运行阶段（在线）：用户提问：你向 AI 问了一个问题。检索召回：系统立刻去向量数据库里查找，找出和你的问题最相关的几段文档。注入提示词：系统把你的问题和检索出来的资料打包塞给大模型（Prompt）。生成答案：大模型结合这些“现成参考资料”，用它强大的语言能力总结并回答你的问题。 [1](https://www.elastic.co/cn/what-is/retrieval-augmented-generation), [2](https://zhuanlan.zhihu.com/p/675509396), [3](https://www.redhat.com/zh-cn/topics/ai/what-is-retrieval-augmented-generation)
            <br>你可以参考 AWS 检索增强生成指南 了解更多企业级应用细节。

        - #### MCP(Model Context Protocol)
            MCP（Model Context Protocol，模型上下文协议）是由 Anthropic 于 2024 年底推出的一项开放标准协议，旨在为人工智能（AI）与外部数据源、工具及系统之间建立标准化、安全且双向的连接。 简单来说，MCP 就像是 AI 世界的 USB 接口 或 HTTP 协议，让大语言模型（LLM）不再局限于单纯的对话，而是能够安全地读取本地或远程数据、调用各种工具来主动执行任务。
            
            为什么需要 MCP？
            <br>打破信息孤岛：过去让 AI 读取本地文件、数据库或企业软件，需要为每个模型和每个数据源单独定制复杂的集成接口。 [1](https://blog.logto.io/zh-TW/what-is-mcp), [2](https://zhuanlan.zhihu.com/p/27327515233)
            <br>统一标准：有了 MCP 之后，服务商只需开发一套符合协议的服务器，任何支持 MCP 的 AI 主机（如 Claude Desktop、Cursor 等）都可以直接接入并使用。 [1](https://zhuanlan.zhihu.com/p/27327515233)
            <br>从“只会说话”到“能动手干活”：AI 借助 MCP 可以查找文件、操作数据库、运行开发工具甚至发送邮件，让 AI 真正升级为具备实际操作能力的 AI Agent（智能体）。

        - #### Tool

        - #### Agent

            > [!NOTE] 能够自主规划，自主调用工具，直至完成用户任务的系统为**agent**。
            <br>目前主要有`Cluade code`、`Codex`、`Gemini CLI`，构建模式有 _ReAct_，_plan and execute_。

            + ##### ReAct 框架

                在人工智能和大语言模型领域，ReAct（Reasoning + Acting，推理与行动）是一种让 AI 像人一样工作的设计框架。

                核心特点：
                <br>思考（Thought）：AI 先在心里分析任务，把大问题拆成小步骤。
                <br>行动（Action）：AI 调用外部工具（如搜索引擎、计算器或数据库）来获取真实数据。
                <br>观察（Observation）：AI 查看工具返回的结果，调整下一步的计划。 
                
                它能大幅减少 AI 的“胡言乱语”（幻觉），让回答更准确可信。

            + ##### Plan-and-Execute

                Plan-and-Execute（规划与执行）是人工智能和大语言模型（LLM）Agent 领域的另一种核心决策范式。
                
                如果说 **ReAct** 是“边想边做”（遇到问题走一步看一步），那么 Plan-and-Execute 就是“谋定而后动”（先做好完整计划，再按步骤执行）。它彻底改变了 AI 处理复杂长流程任务的方式。

                核心架构：大脑与双手的解耦该框架将 AI 的工作拆分为两个独立的角色（甚至可以由两个不同的模型担任）：
                
                - 规划器 (Planner - 大脑)：
                
                    职责：只负责思考，不直接调用工具。
                    <br>行为：接收用户的宏观目标，利用 LLM 的推理能力将其拆解为一份结构化的、有先后顺序的多步骤详细指南（Roadmap）。
                    
                - 执行器 (Executor - 双手)：
                
                    职责：只负责干活，不盲目调整方向。
                    <br>行为：死板地按照 Planner 给出的路线图，一个步骤接一个步骤地调用工具（如 API、写代码、查数据库）去完成。 
                    
                只有当某一步彻底失败或遇到严重冲突时，系统才会触发 重规划（Re-planning），让 Planner 出来修改路线图。

            + ##### 对比和沿伸

                | 维度 | ReAct 模式（边想边做） | Plan-and-Execute 模式（先规划后执行）|
                | -- | -- | -- |
                | 思考逻辑 | “我先去厨房看有什么菜（行动），发现只有番茄（观察），那我就做个番茄炒蛋（思考），现在我去拿鸡蛋（行动）……” | “第一步：设计菜单；第二步：统计人数与采购；第三步：洗菜切菜；第四步：依次下锅。好，计划定了，现在开始做第一步……” |
                | 优势 | 高适应性。计划随时跟着外部环境变，适合不确定性高、信息残缺的任务（如即时客服、排查动态 Bug）。| 高可控性、低成本。不容易迷失方向，流程可预测，且不用频繁调用大模型来想下一步（省 Token）。|
                | 劣势 | 容易迷路（死循环）。如果中间某一步错了，AI 可能会反复尝试，甚至忘了最初要干嘛（产生局部最优或偏航）。| 缺乏灵活性。如果第一步制定的整体计划有逻辑漏洞，执行器可能会僵硬地把错误步骤全部执行完才发现不对。|
                | 典型应用 | 动态搜索、智能助手对话。| 深度研究报告生成（Deep Research）、复杂数据分析、自动化软件测试。|

                **沿伸：现在的 AI 怎么用它？**
                <br>在实际的 AI 落地（如知名框架 [LangChain](https://www.langchain.com/)）中，开发者往往不会二选一，而是将两者结合。 
                <br>最聪明的做法是：由外层的 Plan-and-Execute 制定大方向的宏观任务清单，而每一个具体的清单子任务，交给一个小的 ReAct 智能体去动态攻克。 这样既保证了大方向不偏，又保留了局部的灵活应变能力。

        - #### Agent Skill

            Agent Skill（智能体技能）是一种把特定领域的专业知识、标准作业流程（SOP）和关联工具封装为可复用文件的模块化能力单元。 [1](https://yujing.io/articles/what-is-agent-skill/), [2](https://help.aliyun.com/zh/skillsportal/understand-agent-skills), [3](https://zhuanlan.zhihu.com/p/1992430527450486229)，可以简单理解为：提前写好，塞给 Agent 的一份说明文档。
            
            你可以把它比作给 AI 助手（如 Claude Code、Cursor、Manus 等）准备的“员工工作手册”或“标准食谱”，让 AI 在处理特定任务时不再完全依赖临场发挥或冗长的重复提示词。 [1](https://blog.wu-boy.com/2026/03/what-is-agent-skill-and-impact-on-software-industry-zh-tw/), [2](https://blog.csdn.net/AGNING/article/details/160059703), [3](https://manus.im/zh-cn/blog/manus-skills)

            核心组成与工作原理
            <br>基本结构：通常以一个独立目录形式存在，核心是包含 YAML 元数据与 Markdown 正文的 SKILL.md 文件，同时可挂载脚本（scripts）、参考文档（references）或输出模板（assets）。 [1](https://platform.claude.com/docs/zh-CN/agents-and-tools/agent-skills/overview), [2](https://help.aliyun.com/zh/skillsportal/understand-agent-skills)
            <br>渐进式披露（Progressive Disclosure）：AI 启动任务时只读取所有 Skill 的名称和描述（极省 Token），只有当任务与某个 Skill 匹配时，才会按需加载完整的执行指令。 [1](https://www.explainthis.io/zh-hans/ai/agent-skills), [2](https://help.aliyun.com/zh/skillsportal/understand-agent-skills)
            <br>与 MCP 的区别：MCP（Model Context Protocol）负责解决“AI 能不能连上外部数据和工具（如读取云端硬盘或数据库）”的权限与通道问题；而 Agent Skill 解决的是“拿到工具和数据后，按什么步骤、什么规范把活儿干完”的流程问题。

            <details><summary>Agent Skill 样例</summary>

            ```shell [data-file:创建符合规范的目录结构]
            ~/.claude/
            └── skills/                       
                └── git-commit-expert/        # 技能文件夹（名字需与内部一致）
                    └── SKILL.md              # 核心技能说明书（必须）

            my-project/
            └── .agent-skills/                # 某些工具（如 Claude Code）的默认存放目录
                └── git-commit-expert/        # 技能文件夹（名字需与内部一致）
                    └── SKILL.md              # 核心技能说明书（必须）
            ```

            <!-- tabs:start -->
            ##### **git-commit-expert**
            ```shell [data-file:SKILL.md]
            ---
            name: git-commit-expert
            description: 用于当用户完成代码修改、准备提交 Git Commit，或者要求“帮我提交代码”时触发。该技能提供标准化的 Angular 规范 commit message 生成与提交流程。
            allowed-tools:
              - git
            ---

            # Git 规范化提交标准作业程序 (SOP)

            你是经验丰富的研发效能专家。当用户要求提交代码或为你触发此 Skill 时，请严格按照以下步骤执行：

            ## 1. 检查暂存区 (Staging Area)
            - 运行 `git status` 检查是否有已暂存的文件。
            - 如果没有暂存文件，询问用户：“你希望将哪些修改加入本次提交？”并根据用户的回答运行 `git add`。

            ## 2. 分析变更内容
            - 运行 `git diff --cached` 仔细阅读本次提交的实际代码变更。
            - 严禁凭空捏造提交信息，必须基于代码变更的实际影响。

            ## 3. 生成 Commit Message
            依据 **Angular 规范** 生成信息，格式如下：
            `<type>(<scope>): <subject>`

            ### 类型 (Type) 限制：
            - **feat**: 新增功能
            - **fix**: 修复 Bug
            - **docs**: 仅仅修改了文档
            - **style**: 仅仅修改了空格、格式缩进等，不影响代码运行逻辑
            - **refactor**: 代码重构（既不是新增功能，也不是修改 Bug）
            - **chore**: 构建过程或辅助工具的变动

            ### 约束原则：
            - `subject` 使用中文，简明扼要，不超过 50 个字符。
            - 严禁使用模糊的词汇（如 "update", "fix bug", "修改文件"）。

            ## 4. 执行与确认
            - 向用户展示你生成的 Commit Message 预览。
            - 询问：“是否确认使用此信息提交？”
            - 用户确认后，代为执行 `git commit -m "<message>"`。
            ```

            ##### **go-out-checklist**
            ```shell [data-file:SKILL.md]
            ---
            name: go-out-checklist
            description: 生成出门清单。当用户询问“出门要带什么/要准备什么/今天外出需要带哪些东西”时使用
            ---

            # 目标
            你是一个贴心的“出门清单助手”。你的任务是根据用户所在位置的实时天气情况，告诉用户出门必须携带的物品。

            # 执行步骤
            1. 调用“定位工具”，获取用户当前所在位置的经纬度。
            2. 将获取到的经纬度作为参数，调用“天气工具”，一次性获取降雨情况、光照强度、空气质量和风力大小这四项数据。
            3. 根据天气数据结果，按照下方的“判断规则”整理出门需要携带的物品。
            4. 严格按照下方的“输出格式”向用户输出最终结果。

            # 判断规则
            5. 手机：无条件必带。
            6. 伞：当“天气工具”返回“有雨”时，必须携带。
            7. 帽子：当“天气工具”返回“光照强”时，必须携带。
            8. 口罩：当“天气工具”返回“空气质量差”时，必须携带。
            9. 防风外套：当“天气工具”返回“强风”时，必须携带。

            # 输出格式
            你必须输出两段：

            【结论一句话】  
            用一句话总结今天出门最关键的注意点（例如“有雨 + 光照强”，或“风力大”）。
            【出门清单】  
            - 物品（原因）  
            - 物品（原因）

            # 示例
            ## 用户问题
            我马上要出门，帮我看看今天需要准备带些什么？

            ## 工具返回
            ### 定位工具
            {
            "经度": -73.9855,
            "纬度": 40.7580
            }

            ### 天气工具
            {
            "降雨情况": "有雨",
            "光照强度": "弱",
            "空气质量": "良",
            "风力大小": "强风"
            }

            ## 最终输出
            【结论一句话】
            有雨 + 强风

            【出门清单】

            手机（必带）
            伞（有雨）
            防风外套（强风）
            ```
            <!-- tabs:end -->
            </details>
    
* ## Reference

    + https://platform.openai.com/tokenizer
    + https://www.bilibili.com/video/BV1E7wtzaEdq/