---
title: "\U0001F5B2 逃离信息茧房 - CyberV"
---

## 当今，我们活在一个算法统治的互联网里。

AI 灭绝人类，还太遥远；AI 主宰人类，业已开始。

---

标题说，逃离信息茧房，可信息茧房是什么？我将怎么带你们逃离信息茧房？CyberV 又是什么？

### 信息消费个人化

现在各大内容平台背后都有强大的算法支撑，以带给在座诸位他们想要看到的内容。听起来很好，不是么？——但是，在这同时，他们的第一目的其实是让使用者尽可能地消费更多东西：内容、商品、时间，因而提高其流量收益。

在我们某宝时，首页基本是前一次搜索物品的同款；在我们某乎时，搜索或者点赞、评论、收藏了某内容，接下来会越来越多推荐相似的内容；在我们抖 y 时，看到的新内容本质都是相似的；

![facebook](https://knowscount-1304485449.cos.ap-shanghai.myqcloud.com/img/facebook.jpeg)

所以，你可以为自己打造一份「个人日报」。暂且不恐怖吧？是的，还不恐怖。

### 消费内容重复化

不过，这在满足你的大脑反馈机制的同时，这种以用户画像实现的个性化也会编织一个「信息茧房」——内容的同质化。更详细一些：

> 信息茧房是指人们关注的信息领域会习惯性地被自己的兴趣所引导，从而将自己的生活桎梏于像蚕茧一般的“茧房”中的现象。 由于信息技术提供了更自我的思想空间和任何领域的巨量知识，一些人还可能进一步逃避社会中的种种矛盾，成为与世隔绝的孤立者。

人们更愿意接触自己感兴趣的内容，这受人的天性或者说思想惰性的制约，而技术公司就是利用了这样的弱点，于无形之中诱导你进入信息密闭的空间，把你困在一样的声音里，与真正的社会脱钩。怎么样，恐怖了么？

### 消费群体割裂化

这会造成什么？——网络空间巴尔干化，把所有人分裂成有着不同利益目标、价值信仰、兴趣取向的繁多群类，并将所有人困在自己的茧房里。

---

标题又说，「逃离」信息茧房，我们为什么要逃离？要怎么逃？

### 为什么？

信息茧房能给世界带来无尽的伤害，随便举几个例子：

_个人认知窄化、族群极化、虚假信息泛滥、影响社会稳定……_

### 平台算法的优化

在算法理论中，有一个 EE 问题（Exploration and Exploitation tradeoff），直接译过来就是「探索-利用」问题。

![C_DYJQNW0AAGSpI](https://knowscount-1304485449.cos.ap-shanghai.myqcloud.com/img/C_DYJQNW0AAGSpI.jpg)

有一段来自 Manifold Inc. 的很有意思的 python 代码就是模拟这个情况的：

```python
"""
Short description - This module simulates a 10 armed bandit problem using a simple tabular actioin-value method
:copyright: 2018 Manifold Inc.
:author: Rajendra Koppula <rkoppula@manifold.ai>
"""
import gym
import gym_bandits
import logging
import numpy as np
import os
import pandas as pd
from dotenv import load_dotenv, find_dotenv
from src.visualization.visualize import plot_rewards, plot_actions

N_STEPS_PER_PLAY = 3000

class Agent():
    def __init__(self, env, start_epsilon, end_epsilon=None):
        self.env = env
        self.start_epsilon = start_epsilon
        self.end_epsilon = end_epsilon

    @staticmethod
    def get_greedy_action(df):
        if len(df) != 0:
            Q = df.groupby("a").mean()["r"]
            # Some actions may not have been taken so far. Reindex to add them to the mix
            Q = Q.reindex(range(10), fill_value=0.0)
            # If there are multiple actions with values equal to maximum, random sample one
            maxs = Q[Q==Q.max()]
            if len(maxs) > 1:
                return maxs.sample().index[0]
            else:
                return Q.idxmax()
        else:
            # If empty, choose a random action
            return np.random.randint(10)

    def play(self, n_steps):
        # Action, reward and epsilon for each step
        df = pd.DataFrame(np.zeros((n_steps, 3)), columns=["a", "r", "e"], index=range(n_steps))
        df['a'] = df.a.astype(int)

        # Setup up epsilon decay
        if self.end_epsilon is None:
            # No decay
            df["e"] = np.linspace(self.start_epsilon, self.start_epsilon, n_steps)
        else:
            df["e"] = np.linspace(self.start_epsilon, self.end_epsilon, n_steps)

        # Run for n_steps
        # At each step, make a decision to take a random action or a greedy action
        # Greedy => select the action with the highest estimate of the Expected return
        for step in range(n_steps):
            if df.loc[step, "e"] == 0:
                # greedy policy
                # use history upto step-1
                action = Agent.get_greedy_action(df.loc[:step-1])
            else:
                # epsilon-greedy policy
                e_sample = np.random.random()
                if e_sample <= df.loc[step, "e"]:
                    # Explore by taking a random action
                    action = np.random.randint(0, 10)
                else:
                    # Exploit current knowledge about the system
                    action = Agent.get_greedy_action(df.loc[:step-1])

            _, r, _, _ = self.env.step(action)
            df.loc[step, "r"] = r
            df.loc[step, "a"] = action

        return df


def play_wrapper(name, env, agent):
    avg_r_col = name+' avg reward'
    action_col = name+" action"
    columns = [avg_r_col, action_col]

    df = agent.play(n_steps=N_STEPS_PER_PLAY)
    # Compute average reward recieved up until every step
    df[avg_r_col] = df.r.cumsum()/(df.index + 1)
    df = df.rename(columns={"a":action_col})
    # df = df[columns]

    return df

if __name__ == "__main__":
    load_dotenv(find_dotenv())
    env = gym.make("BanditTenArmedGaussian-v0")
    env.reset()

    # epsilon = 0 => No exploration, always greedy
    agent = Agent(env, 0)
    logging.info("Training greedy agent")
    g_df = play_wrapper('greedy agent', env, agent)

    # Note: Since the steps are IID in 10-armed bandit, we continue to use the same env without resetting it This allows
    # us to evaluate different agents consistently on the same problem.  Resetting env would change the true mean
    # returns for each action. In this case, to properly evaluate we would need to average across many plays for each
    # agent to compare them.
    logging.info("Training epsilon-greedy agent")
    agent = Agent(env, 0.1)
    epsilon_g_df = play_wrapper('epsilon-greedy agent', env, agent)

    # decaying epsilon
    logging.info("Training decaying epsilon-greedy agent")
    agent = Agent(env, 0.1, 0.01)
    decaying_epsilon_g_df = play_wrapper('decaying epsilon-greedy agent', env, agent)

    df = pd.concat([g_df, epsilon_g_df, decaying_epsilon_g_df], axis=1)

    rewards_plot_filename = os.path.join(os.environ['PROJECT_DIR'], "reports/figures/10_armed_agents_rewards.html")
    plot_rewards(df, filename=rewards_plot_filename)

    actions_plot_filename = os.path.join(os.environ['PROJECT_DIR'], "reports/figures/10_armed_agents_actions.html")
    plot_actions(df, filename=actions_plot_filename)
```

在这个的测试下，他们得到了这样的结果：

![rewards1](https://knowscount-1304485449.cos.ap-shanghai.myqcloud.com/img/rewards1.png)

在推荐算法中，第一个 E 代表 Explore，也就是探索用户新兴趣，扩展用户认知；第二个 E 代表 Exploit，也就是通过已有的用户画像利用、开发用户的时间、金钱。

第一个 E 越大，则短期收益越低，长期体验更好；第二个 E 越大，则短期收益越高，茧房效应越强。猜猜资本选择了哪个？——在垄断、竞争的压力、行业巨头的规则下，只能竭尽全力地提高第二个 E，第一个 E 沦为了做给市场、投资方看的道具。

所以，平台并不想摒弃信息茧房。很多平台在不论政府还是投资方的压迫下，尝试扭转这一局面，但用户数量众多且调整难度大，打破信息茧房的尝试就集中在信息供给方——媒体与平台，尤其集中于平台算法机制方面，平台算法的优化，只是减轻信息茧房，平衡信息茧房的因与果，无法消灭信息茧房。

### 如何逃脱？

CyberV。

---

## 有用的链接

[强化学习总结（1）--EE 问题](ttps://www.cnblogs.com/xiachongkun/p/7719235.html)
[manifold 代码](https://github.com/manifoldai/reinforcement_learning_public/blob/master/src/models/k_armed_bandit.py)
[manifold 文章](https://www.manifold.ai/exploration-vs-exploitation-in-reinforcement-learning)
