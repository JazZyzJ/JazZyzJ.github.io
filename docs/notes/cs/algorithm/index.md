---
comment: true
---

# Data Structure and Basic Algorithms

!!! info "intro"
    This chapter includes my notes in **FDS** and **ADS** course.
    
    maybe I will add more advanced and interesting algorithms and DS later 😋 

!!! abstract "Table of contents"
    
    - [x]  [FDS](../algorithm/fds/fds.md) ( ~~FDS 学的不太认真捏，最后考试也被狠狠制裁了~~ )
    !!! Tip "tips"
        ADS课程中前半段数据结构部分我是在yy老师班上听课的，杨老师自己是竞赛选手，对于数据结构的理解不会那么抽象，会采用更好懂，更可视化的方式来讲解，上课体验很不错🤓
    - [x] [ADS](../algorithm/ads/ADS.md)
    - [x] [Backtracking](../algorithm/backtracking.md)
    - [x] [Divide & Conquer](../algorithm/DandC.md)
    - [x] [Dynamic Programming](../algorithm/dp.md)
    - [x] [Greedy Algorithm](../algorithm/gdy.md)
    - [ ] [P/NP](https://note.noughtq.top/algorithms/ads/10.html) 这一部分我学习的时候没有学明白，采用的是NoughtQ同学的笔记和wyy的讲义
    !!! tip "tips"
        以下部分内容的学习我从yy老师班转向了myc老师班，最近几年毛哥出卷子占比挺高，他更喜欢公理化的讲授方法，在算法分析部分能更严谨的对定理进行证明并推广，我就转向毛哥的智云了 ~~从期末考试结果反馈这个选择不错~~
    - [x] [Approximation](../algorithm/Approximation.md)
    - [x] [Local Search](../algorithm/localsearch.md)
    - [x] [Randomized Algorithms](../algorithm/random.md)   
    - [x] [Parallel Algorithms](../algorithm/para.md)
    - [x] [External Algorithms](../algorithm/external.md) 
---


这个部分的笔记我是在大二上学期进行ADS学习时开始定期在Space上上传更新的，刚开始我也只是为了监督自己认真学习ADS，~~毕竟大家都知道ADS的恐怖之处，~~，事实证明这样做确实有效，但也会耽误大量的时间对课程内容进行整理，这一点大家见仁见智，从最终的结果来看，我不仅收获了自己满意的成绩，还拥有了一个未来能回顾纪念的精美的ADS笔记，还是很感谢坚持了一学期的自己☺️

- 如果你来这里是为了ADS的学习，那么我有以下的一些小建议：

1. **课堂**：除了上面提到的老师选择外，包括张国川老师等等各位老师都是十分认真并且高水平的在进行授课，因此无论你选择的是什么老师（包括智云的选择），我都建议你在上课时一定尽力听懂，至少知道自己不懂什么，下课才能更有效率的回顾整理
2. **回顾**：我作为非OI选手，在第一次学习课上的绝大部分知识时，掌握率都不到50%，因此我强烈建议大家在课下再进行笔记的整理，而不是课上就记笔记，一是课上可以全神贯注的听课理解，二是课下重新整理能更加清晰起到复习作用（当然我身边也有完全不上课只听智云的例子，我在听毛哥课时也采取了这种策略，我觉得还不错，但这需要很强的自制力，这里我不对这两个方法的优劣进行比较）
3. **复习**：一方面理解课上的知识，对我们以后的科研/工作可能会有奠基性的作用，另一方面这门课的考试是十分地狱的👺，因此在期末周时，你迫切的需要面向考试的复习策略：

    - 知识梳理：我的建议是快速的过一遍自己或者别人的笔记，进行回顾，然后用基础题进行检测
    - 自测：这里推荐一些前辈的整理[xyx的考试技巧](https://www.yuque.com/xianyuxuan/coding/ads_exam_1)[oneko的题目集](https://www.yuque.com/oneko/something/iloveads)这两个题目集相互补充，不看解析自己做完这两个基本上就没问题
    - 技巧：考试中稳住心态先保证做出来简单题，编程题我当时是直接选择打点测试样例的，~~居然拿到4分，感谢yy哥~~，对于难题大胆用边界假设然后排除，可以提高正确率