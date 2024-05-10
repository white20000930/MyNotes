MIT Distributed Systems

# Lecture 1

## Paper 1: MapReduce

> MapReduce: Simplified Data Processing on Large Clusters

### Abstract

MapReduce 是什么？

- 编程模型和相关实现
- 用于处理和生成大型数据集
- 用户指定
  1. map function，函数处理一组key/value pair以生成一组intermediate key/value pair
  2. reduce function，合并与同一intermediate key相关的所有中间value

运行时系统做什么？  The run-time system

处理这些细节，对输入数据进行分区，跨多台机器调度程序的执行，处理机器故障，管理所需的机器间通信

### Execution Overview

Map

- 通过自动将输入的数据分成$M$个splits
- 在多台机器上分布执行，多个机器并行处理这些splits

Reduce

- 使用分区函数partitioning function，将intermediate key空间划分为$R$个部分，来进行分布

# code

### word-count

思路

1. 读取文件
2. map，划分单词。{ (单词，"1") }
3. Sort，按照单词排序。{ (单词a，"1"), (单词a，"1"), (单词b，"1"), (单词c，"1") }
4. for遍历
   1. 相同的单词放入同一切片。{ (单词a，"1"), (单词a，"1")  },    { (单词b，"1") },  { (单词c，"1") }
   2. reduce，统计频数（切片长度）。{ (单词a，"1"), (单词a，"1")  } -> 2
   3. 打印输出

### Job

1. 实现分布式的MapReduce，由一个coordinator和多个worker组成，通过RPC通信
2. worker







1. “Map 阶段应将中间键分配到 nReduce 个 reduce 任务的桶中，其中 nReduce 是 reduce 任务的数量 -- 这是 main/mrcoordinator.go 传递给 MakeCoordinator() 的参数。每个 mapper 应创建 nReduce 个中间文件供 reduce 任务使用。“在单词计数这个例子中，我的nReduce应该是不同单词的数量，但根据这段话，nReduce好像是指定的，在程序中指定为10,那怎么用呢？
2. 工作器实现应将第 X 个 reduce 任务的输出放在文件 mr-out-X 中。
3. 一个 mr-out-X 文件应包含一行 Reduce 函数的输出。这行应使用 Go 的 "%v %v" 格式生成，调用键和值。可以在 main/mrsequential.go 中查看注释 "this is the correct format" 的行。如果你的实现与这种格式偏离太多，测试脚本将会失败。
4. 你可以修改 mr/worker.go、mr/coordinator.go 和 mr/rpc.go。你可以临时修改其他文件进行测试，但请确保你的代码能与原始版本一起工作；我们将使用原始版本进行测试。
5. 工作器应将中间 Map 输出放在当前目录的文件中，你的工作器稍后可以将它们作为 Reduce 任务的输入读取。
6. main/mrcoordinator.go 期望 mr/coordinator.go 实现一个 Done() 方法，当 MapReduce 作业完全完成时返回 true；此时，mrcoordinator.go 将退出。
7. 当作业完全完成时，工作器进程应退出。实现这一点的一个简单方法是使用 call() 的返回值：如果工作器无法联系到协调器，它可以假设协调器已经退出，因为作业已经完成，所以工作器也可以终止。根据你的设计，你可能也会发现让协调器给工作器一个 "请退出" 的伪任务很有帮助。





- 指导页面上有一些关于开发和调试的提示。
- 入门的一种方式是修改 mr/worker.go 的 Worker()，向协调器发送一个 RPC 请求，请求一个任务。然后修改协调器以响应尚未开始的 map 任务的文件名。然后修改 worker 以读取该文件并调用应用程序的 Map 函数，如 mrsequential.go 中所示。
- 应用程序的 Map 和 Reduce 函数在运行时使用 Go 的插件包从以 .so 结尾的文件中加载。
- 如果你在 mr/ 目录中更改了任何内容，你可能需要使用类似于 go build -buildmode=plugin ../mrapps/wc.go 的命令重新构建你使用的任何 MapReduce 插件。
- **这个实验依赖于 worker 共享文件系统。当所有的 worker 都在同一台机器上运行时，这是简单的，但是如果 worker 在不同的机器上运行，就需要像 GFS 这样的全局文件系统。**
- 中间文件的合理命名约定是 mr-X-Y，其中 X 是 Map 任务的编号，Y 是 reduce 任务的编号。
- worker 的 map 任务代码需要一种方式将中间键/值对存储在文件中，以便在 reduce 任务中正确读回。一种可能性是使用 Go 的 encoding/json 包。将键/值对以 JSON 格式写入打开的文件：

- 你的 worker 的 map 部分可以使用 worker.go 中的 ihash(key) 函数来为给定的键选择 reduce 任务。
- 你可以从 mrsequential.go 中借用一些代码来读取 Map 输入文件，对 Map 和 Reduce 之间的中间键/值对进行排序，以及将 Reduce 输出存储在文件中。
- 作为 RPC 服务器的协调器将是并发的；不要忘记锁定共享数据。
- 使用 Go 的 race detector，使用 go run -race。test-mr.sh 在开始处有一条注释，告诉你如何使用 -race 运行它。当我们为你的实验打分时，我们不会使用 race detector。然而，如果你的代码有竞态条件，即使没有 race detector，我们在测试时也很可能会失败。
- Workers 有时需要等待，例如，reduce 不能在最后一个 map 完成之前开始。一种可能性是让 workers 定期向协调器请求工作，在每次请求之间使用 time.Sleep() 休眠。另一种可能性是让协调器中相关的 RPC 处理器有一个等待的循环，可以使用 time.Sleep() 或 sync.Cond。Go 在自己的线程中运行每个 RPC 的处理器，所以一个处理器正在等待并不会阻止协调器处理其他 RPC。
- 协调器不能可靠地区分崩溃的 workers，活着但由于某种原因停滞的 workers，以及执行速度过慢以至于无用的 workers。你能做的最好的事情就是让协调器等待一段时间，然后放弃并将任务重新分配给另一个 worker。对于这个实验，让协调器等待十秒钟；之后协调器应该假设 worker 已经死亡（当然，它可能没有）。
- 如果你选择实现备份任务（第3.6节），请注意我们测试你的代码在 workers 执行任务时不会安排额外的任务。备份任务应该只在相对较长的时间（例如，10s）后被安排。
- 为了测试崩溃恢复，你可以使用 mrapps/crash.go 应用程序插件。它在 Map 和 Reduce 函数中随机退出。
- 为了确保在崩溃的情况下没有人观察到部分写入的文件，MapReduce 论文提到了使用临时文件并在完全写入后原子性地重命名它的技巧。你可以使用 ioutil.TempFile（或者如果你正在运行 Go 1.17 或更高版本，可以使用 os.CreateTemp）来创建一个临时文件，并使用 os.Rename 来原子性地重命名它。
- test-mr.sh 在子目录 mr-tmp 中运行所有的进程，所以如果出了问题，你想查看中间或输出文件，就在那里查看。随时可以临时修改 test-mr.sh，在失败的测试后退出，这样脚本就不会继续测试（并覆盖输出文件）。
- test-mr-many.sh 连续多次运行 test-mr.sh，你可能想这样做以便发现低概率的错误。它接受一个参数，表示运行测试的次数。你不应该并行运行多个 test-mr.sh 实例，因为协调器会重用同一个套接字，导致冲突。
- Go RPC 只发送字段名以大写字母开头的结构字段。子结构也必须有大写的字段名。
- 在调用 RPC 调用 call() 函数时，回复结构应包含所有默认值。RPC 调用应该像这样：
