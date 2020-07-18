## Thoughts
- http://wargame.kr:8080/qr_code_puzzle/
- Key : ba3a60c1afd94f97afce45023748363fa5265efa
1. Was an interesting problem, the 'longest' time I've spent on CTF so far.
2. That's probably because this is the 'SECOND' CTF I've ever solved (attempted?)
3. And man, I HAD the QR code image during the debugging / hacking process, but it never occured to me that the QR code itself would contain the link / key for the Flag.
4. I was expecting something to happen inside the page, like from a javascript function, so actually my main focus while debugging was to actually figure out what 'finishGame' function would do.
5. But, after hours of (maybe in total, 2 hours?) learning stuff about jQuery and figuring out why the heck I couldn't access that finishGame function from the console, I learned that there was no redirection stuff happening inside.
6. Moving all the javascript and html files on a local device and editing those two files was probably the BIGGEST productivity booster for me.
7. Toying with things and actually breaking them, tampering them taught me a lot.

## Things I learned
1. First of all, I learned that `function() { ... }` is a 'function constructor' for javascript. At first I thought it was weird (most likely because in C, constructor always has paranthesis for taking the input, and hmm I mean, the JS version also has that, but the '{}' part is a big difference for sure.) but after reading some helpful materials, I realized that it's just a constructor, and that's the way JS rolls.
 - https://www.w3schools.com/js/js_function_definition.asp

 2. What `jQuery.fn` or `$.fn` means.
 - So, the jQPuzzle library itself is like this:
 `(function($) {
    $.fn.jqPuzzle = function(settings, texts) {
        ...
    }
 )(jQuery)`
 - At the first glance, nothing makes sense. What is '$'? And why is there '(jQuery)' at the end?
 - As it turns out, this whole thing, with the form `(function(){ ... }()) is called *'Immediately Invoked Function Expression'*.
 - And so that function gets executed immediately.
 - But the 'jQuery' part is actually for protecting the '$' alias which equals the global 'jQuery' instance, but can have conflict with the '$' used in plain javascript libraries.
 - https://learn.jquery.com/plugins/basic-plugin-creation/
 : This Plug-in tutorial discusses the $ alias protection AND Chaining (another fascinating concept)
 - OH, so what 'fn' does. jQuery.fn (or, in jQuery's terms, equivalent to $.fn) actually points to the jQuery's main prototype itself.
 - And just like you can add another variable to a jQuery object like : `object.b = 'hey';`, this .fn allows you to add your custom 'function' that can be accessed globally from jQuery instances.
 - SO, actually in the CTF code, the HTML file actually contains : `$('#join_img').jqPuzzle( ... ); hide_pz();});`. And the reason why .jqPuzzle function works, is because it was configured as a global function for jQuery instances in the library (which, is kind of like a plug-in).
 - https://stackoverflow.com/questions/2937227/what-does-function-jquery-mean
 - https://stackoverflow.com/questions/4083351/what-does-jquery-fn-mean : This explains the .fn very well.
 - https://github.com/jquery/jquery/blob/1.7.1/src/core.js#L76 : And here, you can actually verify that the jQuery.fn EQUALS jQuery.prototype EXPLICITLY. I was surprised to find that jQuery was an open-source project. Interesting stuff.
 
 3. jQuery's this, $(this).
 - In C++, 'this' is pretty straightforward. You use them only when you are inside a class (as far as I know), but in this case, it was used inside a javascript library. And I was curious as to what it pointed towards exactly.
 - There actually is a 'timer' defined inside the library, `var Timer = function(interval, callback) {` and inside that function, are other NESTED functions which refers to this timer as 'this'.
 - I find nested functions & usage of 'this' really fascinating about Javascript.
 - https://stackoverflow.com/questions/7479282/this-vs-this
 - So what is the difference between $(this) and this?
 - Basically, $( ... ) CREATES an jQuery instance, and that is DIFFERENT from a typical DOM object. Remember, jQuery is something you 'add' to the HTML & Javascript combo. It is a cherry on top. So, it's '$( ... )' command itself has it's own purpose. Which, in this case, is creating a jQuery instance.
 - So presumably, $(this) would be slower than this. But, there are times when you need to use a jQuery object, so having two different ways to refer to an object is an interesting point.
 - https://stackoverflow.com/questions/12481439/jquery-this-keyword
 - I actually experimented with this a little bit, for example in this part : `return this.filter('img').each(function() {
            console.log('For Each!')
            console.log(this)
            console.log($(this))`
- And I found that (in this specific example) 'this' referred to the original '<img />' object from HTML, and '$(this)' was just the jQuery object-ified version of it. Which appears as : `init [img#join_img, context: img#join_img]` through Chrome's developer tools. The 'init [ ... ]' structure itself tells me now that it is a jQuery object.
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this : JS's class and functions BOTH can be referenced with 'this' keyword.

4. jQuery's filter.
- https://api.jquery.com/filter/
- So the 'jQPuzzle' function has the 'return' part, which I found interesting, or maybe, it is just intuitive.
- `return this.filter('img').each(function() {`
- And there, you can observe 'filter' function. So I researched about it, and found out that it returns all the corresponding componnets (jQuery object, of course) from the given component (this, in this case, which refers to the HTML's original <img /> tag object).
- One thing I found fascinating was that you can receive the 'index' of the filtered object through the function, and so if you put `.filter(function(index){console.log(index)})`, you can actually observe that for each item found (filtered), it's index will get printed from the console. Which, in my case was 36-ish, hence 0 ~ 35 as an index.
- This index thing was a big A-HA moment for me, because it totally makes sense to have that sort of 'reference' given by a filtering function, and the fact that you can do whatever you want with that 'index' information, just, felt, right.

## Final Remark
So I guess that's about it. A lot of jQuery and Javascript learning involved, me being a NooB, it was a great learning experience. And even when I wanted to give-up, I didn't. Which made my small victory even more meaningful.
I probably won't forget this CTF for a while. Or maybe, it'll just be the beginning. Overall, nice!

 ## TODO
 - [ ] https://api.jquery.com/jquery.noconflict/ : understand this.
 - [ ] 

 ## Other References
 - https://github.blog/2014-04-28-task-lists-in-all-markdown-documents/
- https://www.researchgate.net/post/How_can_I_call_an_external_javascript_function_in_html_with_a_link
- https://stackoverflow.com/questions/9054870/calling-a-javascript-function-from-console
