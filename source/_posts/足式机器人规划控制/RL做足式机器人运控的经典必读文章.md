---
title: RL做足式机器人运控的经典必读文章
tags:
  - RL
  - Robotics
date: 2025-04-17
---
作者 | Minghuan Liu、Yufei Xue  编辑 | 具身智能之心

原文链接：https://zhuanlan.zhihu.com/p/29806809248
最近人形机器人如火如荼，其中基于强化学习和sim2real的运控算法更是关键。但人形机器人的强化学习运控算法也是从四足机器人的运控算法中迭代而来。对于想要入门的小伙伴，HugWBC一作宇斐 @韬晦待时 和我一起整理了一份学习过程中的关键论文（从四足到人形），并总结了最关键的贡献，供大家入门。如果有表述不当欢迎纠错！

Tips：

- 在如今广泛使用的Isaacgym并行GPU训练环境（21年）之前，此前大多数训练基于ETH开源的RaiSim训练框架，使用CPU采样。而基于Isaacgym的如今广为流传的开源训练代码也基本是基于由ETH开发使用并开源的项目（legged_gym+rsl_rl）。
    
- 文中的Domain randomization≈Dynamics randomization，前者在提出的语境下强调测试和训练时的环境差异，从domain adapation领域而来；后者强调机器人/环境的动态模型属性，从机器人的dynamics状态转移而来，但基本表达的是一个意思，可以混用。
    
- 利用强化学习的足式运控方案主要的难点在于sim2real gap，因为在sim里训练的结果不论多么好，最终是要看real部署的表现的。因此，在方案上，大致经过了SysID->电机模型->Teacher Student->非对称框架，同步引入了domain randomization来帮助解决sim2real的迁移gap
    
- 算法和sim本身能cover的sim2real gap有限，机器人本体的性能参数，例如电气设计、电机稳定性，电机扭矩，以及电机驱动的可用性、延迟，此外，还包括机器人描述文件URDF的准确性，都决定了算法迁移到本体的能力上限，有些甚至直接影响能否迁移。此外，大部分成功迁移的机器人都是串联杆（或转换成近似串联）机构的机器人，这是因为模拟器的限制。大部分并联杆和软体机器人难以被现有广泛应用的刚体模拟器所建模，需要首先开发更准确的模拟器，再考虑实际迁移。
    

首先是来自ETH Robotics System Lab的经典工作，ETH首先利用在ANYmal四足机器人上证明了基于强化学习的运控算法的有效性，这一系列的文章都很有学习价值。

1. **Learning Agile and Dynamic Motor Skills for Legged Robots. Science Robotics, 2019**. 强化学习做四足运控的奠基之作，通过利用real数据训练的电机网络模型在sim中补偿的方式训练策略模型+RL+dynamics randomization，并成功在ANYmal四足机器人上部署，实现了鲁棒的locomotion策略，包括以特定速度奔跑、跌倒恢复等。
    
2. **Learning quadrupedal locomotion over challenging terrain. Science Robotics, 2020**. 同样是强化学习做四足运控的奠基之作，首次提出了两阶段的教师-学生框架，其中教师策略利用模拟器里的作弊信息学习到好的运动策略，再通过在线模仿学习dagger蒸馏给可部署（可观测数据的历史）的学生策略（引入额外的encoder用于估计隐式的作弊信息）。同时提出了地形课程，实现了鲁棒的复杂连续地形行走策略。注意此时ETH的控制策略还比较复杂，网络仅用于辅助足端轨迹生成，还需要借助IK求解得到最终的target joints。现在主流的工作都是直接用策略输出target joints了。
    
3. **Learning to Walk in Minutes Using Massively Parallel Deep Reinforcement Learning. CoRL, 2021**. 真正意义上的里程碑工作，推动了RL+Robots的后续发展。首次开发和验证了IsaacGym并行训练+domain randomization的强化学习运控训练方案。开源了legged_gym项目。
    
4. **Learning robust perceptive locomotion for quadrupedal robots in the wild. Science Robotics, 2022**. 集成了本体感知和外部视觉信号的鲁棒locomotion策略，实现了相当复杂的大规模户外甚至极端条件行走，包括楼梯、隧道、雪山、浓雾、森林、碎岩。将足式的locomotion策略应用范围提高了一个台阶。尤其是给出了学习带外感知策略（估计高程图）的范例。
    
5. **Elevation Mapping for Locomotion and Navigation using GPU. IROS, 2022**. 本文提出了一种利用GPU高效构建高程图的方法，Elevation Mapping通过接收机器外部传感器的点云信息以及机器人的位姿信息快速构建机器人周围的地形高程图，但在复杂环境中里程计漂移的问题仍然是实时建图最大的挑战。代码已开源
    
6. **Advanced Skills through Multiple Adversarial Motion Priors in Reinforcement Learning. ICRA, 2023**. 增强版本的AMP，允许多种可离散切换的风格，在轮腿四足机器人上展示了躲避、行走，以及从在四足和双足模式（站立）之间切换的风格。有一个从反向站立（双足）序列学习趴下（四足）的case比较有意思。
    
7. **Curiosity-Driven Learning of Joint Locomotion and Manipulation Tasks. CoRL 2023**. 主要亮点在于用轮腿四足学了用腿当manipulator的loco-manipulation策略。
    
8. **Learning Agile Locomotion on Risky Terrains. IROS, 2024**. @XueYeiii ：这个工作是我比较喜欢或者是有意思的工作之一，虽然contribution没其他这么大。可能是第一篇基于RL运控走离散地形的工作（虽然不是全向行走），而且demo效果很不错。
    
9. **ANYmal parkour: Learning agile navigation for quadrupedal robots. Science Robotics, 2024**. 本文提出了一种多层级的框架，包括感知模块、导航模块以及运动模块，其中感知模块用于重建环境信息，导航模块则根据环境信息导航四足机器人的运动并驱动运动模块选择合适的运动能力，而运动模块则具备跑酷运动的基本技能。也是首篇实现四足机器人的动态跑酷的文章。
    
10. **DTC: Deep Tracking Control. Science Robotics, 2024**. 本文将Model-based的方法和Learning-based的方法进行了有效的结合，上层的TO模块根据环境和机器人的动力学实时优化机器人的机身轨迹与足端轨迹，底层RL模块严格跟踪上层的优化轨迹，实现了在极端地形的精确运动，包括梅花桩、深沟、野外废墟等环境。该框架改善了基于学习方法在精确控制中的不足，相较于此前绝大多数的工作中将运动控制的任务制定为机身速度跟踪，这在一定程度上牺牲了腿足式机器人灵活选择落足点的优势，而本文所提出的分层的框架则有效改善了这一问题。
    

来自韩国KAIST的机器人学者也开发了很多经典的强化学习运控算法方案，很值得一读。他们主要在Raibo (MIT Mini Cheetah)和宇树的机器狗上验证算法。

1. **Concurrent Training of a Control Policy and a State Estimator for Dynamic and Robust Legged Locomotion. RAL, 2022**. 提出了一阶段的非对称的学习方式（没有利用教师-学生），即策略网络同时训练一个关键状态估计器（linear velocity, foot height, and contact probability，输入是历史可观测数据），而在sim里critic直接拿到关键状态的真实值，同时引入了dynamics randomization，再利用强化学习算法PPO进行训练，最终部署的策略可以成功穿越多样的连续复杂地形。
    
2. **DreamWaQ: Learning Robust Quadrupedal Locomotion With Implicit Terrain Imagination via Deep Reinforcement Learning. ICRA, 2023**. 非对称的强化学习结构，其中状态估计器主要用于从可观测信息中估计速度和隐式地形信息z，利用-VAE来学习。在宇树A1上实现了鲁棒的长距离多样户外地形行走。 证明了非对称演员-评论家的训练框架可以实现四足机器人在复杂环境中的鲁棒运动，其运动能力甚至可以超过特权学习中学生策略的运动能力。
    
3. **Learning quadrupedal locomotion on deformable terrain. Science Robotics, 2023**. 针对当前simulator仅能仿真刚性接触动力学，修改了RaiSim的底层接触动力学计算模型，建模了granular media mode颗粒接触动力学，实现了Raibo在沙滩和海绵垫等松软地形的敏捷和鲁棒运动，此外本文采用了非对称的训练框架。进一步证明了该框架的有效性。此后非对称成为了腿足式机器人强化学习运动控制器的主流框架
    
4. **A Learning Framework for Diverse Legged Robot Locomotion Using Barrier-Based Style Rewards. ICRA, 2025**. 通过引入松弛对数障碍函数方法，设计了一种基于运动模式特征的软约束奖励机制，有效引导机器人自主趋向于预设的运动形态参数，包括周期性步态生成、关节运动范围限制等。值得注意的是本文并未使用运动先验但却生成了多种仿生的四足步态，其中四足站立的仿人行走以及模拟动物奔跑Gallop步态令人影响深刻。
    
5. **Learning-based legged locomotion state of the art and future perspectives. IJRR, 2025**. 本文是一篇基于学习方法的腿足式机器人运动控制的综述文章，系统的概括过去几年内学术界和工业界基于学习方法的腿足式机器人运动控制的研究成果并展望了未来该领域的发展前景和方向。来自Gatech。
    

除了上面两大有代表性的学校，还有很多研究者也在足式机器人的运控算法研究上做出了很有借鉴意义的解决方案，其中包括：

1. **Sim-to-Real: Learning Agile Locomotion For Quadruped Robots**. 同样算强化学习做四足运控的奠基之作，启发了ETH的ANYmal系列，属于较早的一篇四足机器人sim2real工作，基于Ghost Robotics的Minitaur四足机器人。文中提出了利用RL+analytical 解析式电机模型+dynamics randomization解决了sim2real的迁移问题，实现了灵巧的locomotion行为。
    
2. **Learning Agile Robotic Locomotion Skills by Imitating Animals. RSS, 2020**. 在宇树早期的Leika狗上，基于Motion Regtargeting和Deepmimic的motion tracking方案，学习动物自然风格的运动行为。代码已开源。来自UCB
    
3. **Sim-to-Real Learning of All Common Bipedal Gaits via Periodic Reward Composition. ICRA, 2021**. 较早提出了基于概率周期函数定义的足式步态，包括站立、行走、跳跃、跑步和单脚跳，在Cassie上测试。主要为每种步态单一训练了一个策略，以及一个多步态的切换策略。被后续很多工作所参考。
    
4. **AMP: Adversarial Motion Priors for Stylized Physics-Based Character Control. SIGGRAPH, 2021**. 不是做足式机器人的工作，但是框架本身有迁移价值和使用意义。用GAN作为隐式的metric去获得风格引导奖励来学习控制策略。来自UCB
    
5. **RMA: Rapid Motor Adaptation for Legged Robots. RSS, 2021**. 整体方案接近ETH的，teacher-student+adaption module（对应ETH的TCN encoder）。此外，没有使用地形课程，通过随机采样地形+domain randomization+RaiSim并行训练，在宇树的A1上实现了无外感知的复杂连续地形穿越。代码已开源。来自CMU
    
6. **Walk These Ways: Tuning Robot Control for Generalization with Multiplicity of Behavior. CoRL, 2022**. 在宇树的Go1四足机器人上定义并实现了可细粒度调整步态的鲁棒locomotion策略，其中可控参数包括速度、步态（pronking、trotting、bounding、pacing、galloping）、步频、机身高度、俯仰角、触地时间、抬腿高度等。代码已开源。来自MIT
    
7. **Rapid Locomotion via Reinforcement Learning. RSS, 2022**. 本文提出了一种速度网格自适应采样课程，机器人可以根据自生运动能力自大程度的挖掘运动速度的上限，避免了手工调整速度课程，最大限度减少了专家知识的干预。代码已开源。来自MIT
    
8. **Adversarial Motion Priors Make Good Substitutes for Complex Reward Functions. ICRA, 2022**. 首次将AMP的方法应用到四足机器人，机器人仅通过任务奖励和风格奖励就能学会自然的运动行为，推动了后续基于模仿学习四足机器人运动控制的研究。代码已开源。来自UCB
    
9. **Deep Whole-Body Control: Learning a Unified Policy for Manipulation and Locomotion. CoRL, 2022**. 在宇树Go1四足机器人+WidowX机械臂首次利用强化学习运控实现臂-腿的协同控制（基于遥操作）。此外基于RMA提出了ROA将其中的环境隐变量z由两阶段估计变为一阶段在线估计。基于Isaacgym+rsl_rl开发，代码已开源。来自CMU
    
10. **Learning robust and Agile Legged Locomotion Using Adversarial Motion Priors. RAL, 2023**. 使用教师-学生训练框架，在仅用本体感知的情况下在宇树的Go1四足机器人上实现了鲁棒的复杂连续地形行走，亮点在于使用AMP引导四足机器人在复杂地形上也能学习到自然步态运动，其中自然步态运动数据利用轨迹优化在平地上生成。效果非常好，以至于一作直接被挖到宇树 ;) 来自SJTU
    
11. **Lifelike Agility and Play on Quadrupedal Robots using Reinforcement Learning and Generative Pre-trained Models. Nature Machine Intelligence, 2023**. 本文利用生成模型的强大表现能力，首先在预训练阶段学习了来自于动物的大规模运动数据，而在第二阶段的环境层面的学习中，机器人与环境交互的过程中复用第一阶段所学到的运动技能。是腾讯Robotics X的经典代表作。
    
12. **Learning Agile Skills via Adversarial Imitation of Rough Partial Demonstrations**. 从部分不完整的动作演示中学习四足机器人的动态运动，数据集来源于人为手工操作机器人收集，在低成本的机器人上实现了后空翻动作。
    
13. **Extreme Parkour with Legged Robots. ICRA, 2024. 和 Robot Parkour Learning. CoRL, 2024**. 两篇同期工作，用domain randomization + 外感知将宇树A1四足机器人的连续地形locomotion能力调到极致，可以实现跳箱、翻越、夹缝行走等多种行为。来自CMU、Stanford+上海期智
    
14. **Visual Whole-Body Control for Legged Loco-Manipulation. CoRL, 2024**. 在宇树的B1四足机器人+Z1机械臂上首次验证了基于视觉输入的、基于模仿学习和强化学习训练的双层策略、利用sim2real方式实现自动移动抓取任务的可行性（DeepWBC是单层+遥操作，且arm的控制方案不同）。基于Isaacgym+rsl_rl开发，代码已开源。来自UCSD
    
15. **PIE: Parkour with Implicit-Explicit Learning Framework for Legged Robots. RAL, 2024**. 跑酷运动的plus版本，进一步提高了四足机器人运动的动态能力，实现了在挑战环境中的高速运动并成功迁移到了野外环境。来自于ZJU
    
16. **Expressive Whole-Body Control for Humanoid Robots. RSS 2024**. 和 Exbody2: Advanced Expressive Humanoid Whole-Body Control. 2024. 基于motion (sequence) retargeting中学习人形机器人的运动序列。来自UCSD
    
17. **Open-TeleVision: Teleoperation with Immersive Active Visual Feedback. CoRL 2024**. 一个开源的基于VR人形机器人遥操作框架，只包括上半身。后续的Mobile-TeleVision: Predictive Motion Priors for Humanoid Whole-Body Control. 2024. 工作中展示了一种全身控制的方式。其中locomotion的控制器部分结合了Exbody的方式。来自UCSD
    
18. **HOVER: Versatile Neural Whole-Body Controller for Humanoid Robots. ICRA 2025**. 总结了和统一了几种现有的人形机器人全身控制方式（root velocity tracking, joint angle tracking, key-point tracking等）。来自CMU
    
19. **HugWBC: A Unified and General Humanoid Whole-Body Controller for Fine-Grained Locomotion. 2025**. 在人形上实现了可细粒度调整步态的鲁棒普通连续地形locomotion策略，其中可控参数包括步态（走、跳、站、单脚跳）、步频、机身高度、俯仰角、触地时间、抬腿高度等。从这个角度说有点像人形版的Walk these ways，但值得注意的是其基于多项式足端轨迹引导的抬腿奖励所引导出的自然步态，和利用上肢介入课程实现的上肢自由控制（四足不含上肢），使得其可以作为可扩展的基础人形控制器。来自SJTU和上海AI LAB