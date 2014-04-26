The `yield` keyword is used to pause and resume a generator.

# Iterators and Generators

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators

New stuff from JavaScript 1.7: https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/1.7


Generator
---------

This ia a normal function:

    function fun() {
      console.log('Function');
    }

This is a generator function, denoted by an asterisk. The function containing the yield keyword is a generator. Generators are a special kind of function that can be suspended and resumed:

    function *gen() {
      console.log('Generator starts');
      yield 'Generator';
      console.log('Generator ends');
    }

They both are named functions:

    console.log(fun); // [Function: fun]
    console.log(gen); // [Function: gen]

When executing a function:

    fun(); // Function

When executing a generator, nothing happens:

    gen();

Because it returns a generator object:

    var g = gen();
    console.log(g);

A normal function returns `undefined`:

    var f = fun();
    console.log(f);

To execute the generator function, we need `next`:

    var val = g.next();

It will return the value and status (via `done`) of the `yield`:

    console.log(val);

Since there is a `yield`, we need to call `next` again to finish it:

    val = g.next();

We can close the generator:

    if (val.done) {
      g.close();
    }

When we reach to the end of generator, we are done:

g.next(); // Error: Generator has already finished
return;

// TODO: How does this fix the callback?
function *get() {
  var options = { url: 'http://example.com' };
  var data;
  var handler = function (req, res, body) {
    data = body;
  };
  request(options, handler);
  //yeild next;
  console.log(data);
}

// TODO: Once I understand generator, then dive into Koa to understand how it
// TODO: is using generator to make Express better.
