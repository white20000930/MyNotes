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









# Something New

- 
