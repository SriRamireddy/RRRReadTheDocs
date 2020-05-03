==================
Corotines
==================
==================
What is Coroutines
==================
A coroutine is a concurrency design pattern that you can use on Android to simplify code that executes asynchronously. Coroutines were added to Kotlin in version 1.3 and are based on established concepts from other languages.

On Android, coroutines help to solve two primary problems:

1. Manage long-running tasks that might otherwise block the main thread and cause your app to freeze.
2. Providing main-safety, or safely calling network or disk operations from the main thread.

This topic describes how you can use Kotlin coroutines to address these problems, enabling you to write cleaner and more concise app code.
