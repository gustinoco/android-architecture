# todo-mvp-kotlin

This version of the app is called `todo-mvp-kotlin-coroutines` and is based on [todo-mvp-kotlin](https://github.com/googlesamples/android-architecture/tree/dev-todo-mvp-kotlin/). The sample aims to:

* Replace all asynchronous operations with coroutines, which simplify asynchronous programming by providing possibility to write code in direct style (sequentially). 


## What you need

Before exploring this sample, you should familiarize yourself with the following topics:

* The [project README](https://github.com/googlesamples/android-architecture/tree/master)
* The [todo-mvp](https://github.com/googlesamples/android-architecture/tree/todo-mvp) sample
* The [todo-mvp-kotlin](https://github.com/googlesamples/android-architecture/tree/dev-todo-mvp-kotlin/) sample
* Kotlin [coroutine documentation](https://github.com/Kotlin/kotlinx.coroutines/blob/master/README.md#documentation)

# Dependencies
*  kotlin stdlib
*  kotlin-android plugin
*  kotlinx-coroutines-core
*  kotlinx-coroutines-android

## Implementing the app

All functions in `TasksDataSource` replaces with `suspend` functions.

```kotlin
interface TasksDataSource {
    suspend fun getTasks(): Result<List<Task>>
    suspend fun getTask(taskId: String): Result<Task>
    suspend fun saveTask(task: Task)
    suspend fun completeTask(task: Task)
    suspend fun completeTask(taskId: String)
    suspend fun activateTask(task: Task)
    suspend fun activateTask(taskId: String)
    suspend fun clearCompletedTasks()
    suspend fun refreshTasks()
    suspend fun deleteAllTasks()
    suspend fun deleteTask(taskId: String)
}
```

In `todo-mvp-kotlin` sample functions `getTasks` and `getTask` had callbacks with `onTasksLoaded` and `onDataNotAvailable` functions. In `todo-mvp-kotlin-coroutines` sample this functions now return `Result` which can be ether a `Success` or `Error`.

```kotlin
sealed class Result<out T : Any> {
    class Success<out T : Any>(val data: T) : Result<T>()
    class Error(val exception: Throwable) : Result<Nothing>()
}
```

### Testability

To launch coroutines you need to specify a `CoroutineContext`. In tests all coroutines are launched via `runBlocking` or with `EmptyCoroutineContext`.

```kotlin
@Test
fun loadStatisticsWhenTasksAreUnavailable_CallErrorToDisplay() = runBlocking {
    // Tasks data isn't available
    setTasksNotAvailable(tasksRepository)

    // When statistics are loaded
    statisticsPresenter.start()

    // Then an error message is shown
    verify(statisticsView).showLoadingStatisticsError()
}
```

### Code metrics

Files were converted mostly 1:1 from TODO-MVP's Java code.

```
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
Kotlin                          54            944           1604           3107 (3060 in MVP-kotlin)
XML                             34             95            338            816
-------------------------------------------------------------------------------
SUM:                            89           1039           1942           3923
-------------------------------------------------------------------------------
```