# CompletionService

ExecutorService 的 submit 将会返回一个 Future 对象，**但是如果想获得所有任务的执行结果，就得自己去维护这些 Future 对象，而 CompletionService** 就是为了解决这个问题

此外，假设有个场景。出现 A，B 两个任务，任务 A 耗时 20s，任务 B 耗时 10s，如果使用 Executor 批量提交任务，使用 ``get`` 方法，将会阻塞，耗费时间是 20s，原本是 B 先执行完成，却导致了 B 要等待至 A 的阻塞后，所以说引入了 CompletionService 接口。



