# 框架通识

1. 什么是MVC？

   MVC一般分为3个部分、即M(Modal 模型层)、V(View 视图层)、C(Controller 控制层)
  
   * 模型层： 业务数据的处理和存储，数据更新后更新视图
   * 控制层：负责连接 Model 层和 View 层，接受并处理 View 层触发的事件，并在 Model 层的数据状态变动时更新 View 层
   * 视图层： 人机交互接口，一般为展示给用户的界面；
    
   ![image](https://user-images.githubusercontent.com/57474431/180721982-21fa59c2-0c8f-4db9-8b19-eda023ed62bf.png)
   
   
2. MVP是什么？

  * Model 模型层： 只负责存储数据，与 View 呈现无关，也与 UI 处理逻辑无关，发生更新也不用主动通知 View；
  * View 视图层： 人机交互接口，一般为展示给用户的界面；
  * Presenter 管理层： 负责连接 Model 层和 View 层，处理 View 层的事件，负责获取数据并将获取的数据经过处理后更新 View；
  
  ![image](https://user-images.githubusercontent.com/57474431/180727067-62fea581-b64f-432f-abbf-af3bd6edbdda.png)


  

3. 什么是MVVM

  MVVM一般也分为三个部分、模型（Model）、视图（View）、视图模型（View-Model）。
  
  MV同上，VM是指
  
  
# React

1. React Fiber


   React Fiber的核心思想： 最主要的思想就是将任务拆分。
   
   React页面渲染的两个阶段： 
   
    * 调度阶段： 更新数据 生成新的VDOM 然后通过DOM diff算法，得到需要更新的元素。放到队列中，得到更新队列
    * 渲染阶段： React 会遍历更新队列，将其所有的变更一次性更新到DOM上

   
   React 15的架构
   
    * Reconciler 协调层： 负责找出变化的组件
    * Rerender 渲染层：负责将变化的组件渲染到页面上
    * 缺点：Reconciler采用递归的方式创建虚拟DOM，递归过程是不能中断的。如果组件树的层级很深，递归会占用线程很多时间。

   React 16的架构
   
     * Scheduler 调度器： 调度任务的优先级，高优任务优先进入Reconciler；
     * Reconciler 协调器： 负责找出变化的组件：更新工作从递归变成了可以中断的循环过程。Reconciler内部采用了Fiber的架构；
     * Renderer 渲染器： 负责将变化的组件渲染到页面上。

   Fiber 原理
   
     浏览器中 JS 的运行环境是单线程的，因此，一旦有任务耗时过长，就会阻塞其他任务的执行，导致浏览器不能及时响应用户的操作，从而使用户体验下降
     
     在 Fiber 中，会把一个耗时很长的任务分成很多小的任务片，每一个任务片的运行时间很短。虽然总的任务执行时间依然很长，但是在每个任务小片执行完之后，都会给其他任务一个执行机会。这样，唯一的线程就不会被独占，其他任务也能够得到执行机会。
     
