title: Configure grunt-contrib-jshint options with multiple targets
date: 2013-05-30 12:41:29
tags: [grunt-contrib-jshint, grunt, jshint]
---

When linting, different JSHint options are needed for front-end and back-end
JavaScript. We can specify different targets in [grunt-contrib-jshint][]. But
there is something to be careful:

```javascript
jshint: {
  // Task level options
  options: {
    jshintrc: './.jshintrc'
  },
  node: {
    // Target level options
    options: {
      node: true
    }
  },
  jquery: {
    // Target level options
    options: {
      globals: {
        jQuery: true
      }
    }
  }
}
```

I thought the target level options will merge with the task level, but it does
not happen. Need to dig deeper to figure out why `jshintrc` option is not merged
with JSHint options. A quick fix is to load the JSHint JSON file directly:

```javascript
jshint: {
  // Task level options
  options: grunt.file.readJSON('./.jshintrc'),
  node: {
    // Target level options
    options: {
      node: true
    }
  }
}
```

In this case, if `jshint:node` target was run, the merged options will be parsed
to JSHint.

[grunt-contrib-jshint]: https://github.com/gruntjs/grunt-contrib-jshint
