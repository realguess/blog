title: Gulp Task Alias
date: 2014-07-13 12:49:19
tags: [gulp, task, alias, dependency, duplication]
---

[Gulp] API does not have a method to create an alias task. But, there are two ways to do this now:

1. Create a task alias by dependency
2. Create a task alias by duplication


**Dependency**

Gulp dependency system can be used to create an alias of a task:

    gulp.task('task', function(){});
    gulp.task('alias', ['task']);

Here, task `alias` is the alias of the task `task`. When list the tasks, it shows the dependency tree:

    $ gulp --tasks
    Tasks for ~/gulpfile.js
    ├── task
    └─┬ alias
      └── task

When running the tasks, the dependency is obvious:

    $ gulp alias
    Starting 'task'...
    Finished 'task' after 49 μs
    Starting 'alias'...
    Finished 'alias' after 6.83 μs


**Duplication**

Another way to create a task alias is by duplicating the task definition:

    var task = function () {};    
    gulp.task('task' , task);
    gulp.task('alias', task);

But in this way, there is no implicit relationship that will indicates that one task is an alias of another. As you can see from the task list:

    $ gulp --tasks
    Tasks for ~/gulpfile.js
    ├── task
    └── alias

To Gulp, there are two different tasks. But when the alias task is run, the console log is cleaner than the dependency one:

    $ gulp alias
    Starting 'alias'...
    Finished 'alias' after 45 μs

Gulp is still involving, therefore, API might change regarding for creating alias of a task. At the moment, I like the second option. Alias and dependency are two different concepts. Dependency should be left as dependency. Alias is just another name to run the same task.

[Gulp]: https://github.com/gulpjs/gulp
