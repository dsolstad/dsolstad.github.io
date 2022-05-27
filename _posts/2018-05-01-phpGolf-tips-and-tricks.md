---
layout: post
title: Tips and Tricks when Golfing in PHP
categories: [phpgolf]
tags: [codegolf, php]
description: A compilation of tips and trick when golfing in PHP.
---

<a href="https://en.wikipedia.org/wiki/Code_golf">Code golfing</a> is about creating the shortest code, in bytes, to solve a given problem, in a specific language or free of choice. It would be the opposite of <a href="https://xkcd.com/1960/">this</a>. 

Examples of on-going challenges:
* <a href="http://golf.shinh.org">http://golf.shinh.org</a>
* <a href="https://codegolf.stackexchange.com">https://codegolf.stackexchange.com</a>
* <a href="https://www.reddit.com/r/codegolf/">https://www.reddit.com/r/codegolf/</a>
* <a href="https://code-golf.io">https://code-golf.io</a>

This guide was originally written by JWvdVeer and Wim for phpGolf.org. I have done some editing and fine tuning before releasing it again here. Although this guide is written for PHP version 5.3.3, the same principles still applies today, but does not include things like e.g. short array syntax. This is not an exhausting list of all tricks, but these might be the most important ones. The examples in the guide are not meant as the optimal solution for the given problem, but to show off the trick in question. The code written in the guide expects that error reporting is set to E_ALL & ~E_NOTICE.

## General Tips

* PHP behaves in a consistent way, so you can always predict the outcome of the code. Sometimes you might even exploit known odd behavior of PHP.
* Know the environment your code will run in. Most challenges have error_reporting E_ALL & ~E_NOTICE. So notices about functions being deprecated or undefined array indexes are accepted.
* Use Google. Some challenges are copies of other challenges on the Internet. Or they are much the same. So you can get some inspiration of sometimes complete challenges.
* Most of the time the less variables you use will result in a smaller solution in bytes. Ask yourself these questions: Do I really need them, or might the value of this variable also be derived from any other variable? Can I combine two values into one in order to save even 1 byte?


## Numbers

Instead of writing 1000 you can write 1e3. 1000000 would be 1e6 etc.

## Strings

### Substrings
Substrings can be accessed like arrays, which means that you can do this:
{% highlight php %}
<?$a="abc";echo$a[1]; # prints "b"
{% endhighlight %}

instead of this:
{% highlight php %}
<?$a="abc";echo substr($a,1,1); # prints "b"
{% endhighlight %}

### String inversion
Many strings doesn't need to be quoted when notices are turned off (~E_NOTICE), which means that that the following will work, thus saving 2 bytes:

{% highlight php %}
<?$a=HELLO;
{% endhighlight %}

This will however not work:
{% highlight php %}
<?$a=HELLO WORLD;
{% endhighlight %}

If you have a string with whitespace or characters that needs to be quoted, you can invert the string.

This code prints a newline, using 8 bytes:
{% highlight php %}
<?="\n";
{% endhighlight %}

This does the same thing using 7 bytes:
{% highlight php %}
<?="
";
{% endhighlight %}

And finally this does also the same, but using 6 bytes:

{% highlight php %}
<?=~õ;
{% endhighlight %}

Regular expressions are a good example of a kind of strings you can save bytes on using this trick.

Instead of doing this:
{% highlight php %}
<?=preg_filter('#(.)\1+#i','$1','Aa striing  wiith soomee reeduundaant chaars');
{% endhighlight %}

You could save 2 bytes doing this:
{% highlight php %}
<?=preg_filter(~Ü×ÑÖ£ÎÔÜ?,~ÛÎ,'Aa striing  wiith soomee reeduundaant chaars');
{% endhighlight %}

Make sure to set your text editor to latin1 (ISO-8859-1 or Windows-1252) instead of UTF8 otherwise you will save those inverted bytes as multi-bytes which will do the opposite of what we are trying to do here. 

Sometimes you can also invert the input to shorten your overall code.
  
A list of useful inverted characters: 
```
* Tab (char 9) -> ~ö
* Line-feed (char 10) -> ~õ
* Space (char 32) -> ~ß
* '!' (char 33) -> ~Þ
* '"' (char 34) -> ~Ý
* '#' (char 35) -> ~Ü
* '$' (char 36) -> ~Û
* '%' (char 37) -> ~Ú
* '&' (char 38) -> ~Ù
* ''' (char 39) -> ~Ø
* '(' (char 40) -> ~×
* ')' (char 41) -> ~Ö
* '*' (char 42) -> ~Õ
* '+' (char 43) -> ~Ô
* ',' (char 44) -> ~Ó
* '-' (char 45) -> ~Ò
* '.' (char 46) -> ~Ñ
* '/' (char 47) -> ~Ð
* ':' (char 58) -> ~Å
* ';' (char 59) -> ~Ä
* '<' (char 60) -> ~Ã
* '=' (char 61) -> ~Â
* '>' (char 62) -> ~Á
* '?' (char 63) -> ~À
* '[' (char 91) -> ~¤
* '\' (char 92) -> ~£
* ']' (char 93) -> ~¢
* '^' (char 94) -> ~¡
* '_' (char 95) -> ~  (char 160, the fact you are not able to see whitespace doesn't mean that PHP treat is as whitespace!)
```

## Arrays

Unless you're performing an array manipulation, most references to an array index $a[$i] can be replaced with simply $$i. This is even true if the index is an integer, as integers are valid variable names in PHP (although literals will require brackets, e.g. ${0}).

Example of populating an "array":
```php
<?for($i=0; $i<10; $i++) $$i = $i*2;
```

## Control Structures

### Braces
Know where you need brackets and where you don't. If a statement is only one line, you don't need brackets. Compare these two examples that print chars below 1000 that have a "9" in it.
{% highlight php %}
<?for(;++$i<1000;){if(is_int(strpos($i,'9'))){echo$i."\n";}}
{% endhighlight %}

{% highlight php %}
<?for(;++$i<1000;)if(is_int(strpos($i,'9')))echo$i."\n";
{% endhighlight %}

### Multiple statements

Often it happens that you have multiple statements inside an if statement:
{% highlight php %}
<?
if($c%4){
    $q++;
    print$a;
}
{% endhighlight %}

You can rewrite this as:
{% highlight php %}
<?if($c%4&&$q++)print$a;
{% endhighlight %}

Or even better:

{% highlight php %}
<?if($c%4)$q+=print$a;
{% endhighlight %}

Or as optimal as we know it:

{% highlight php %}
<?$c%4?$q+=print$a:0;
{% endhighlight %}

This works because print always returns 1.

### Loops
Never use while loops. For loops are always at least as short as a while loop, and most of the time shorter. The following code is a not very optimized version of rot13.
{% highlight php %}
<?$a="input";while($a[$i]){$b=ord($a[$i++]);echo chr(($b>109?-13:13)+$b);}
{% endhighlight %}

{% highlight php %}
<?for($a="input";$a[$i];print chr(($b>109?-13:13)+$b))$b=ord($a[$i++]);
{% endhighlight %}

Try to use as few control structures as possible. Multiple loops can often be folded into a single loop.

### Ifs
Try to avoid the use of the traditional if-structure. Most times the same action can be done by using the ternary operator. The next three code-snippets are exactly the same:
{% highlight php %}
<?if($i==2)++$j;
{% endhighlight %}

{% highlight php %}
<?$i==2?++$j:0; # Saves one byte.
{% endhighlight %}

{% highlight php %}
<?$i-2?:++$j; # Saves another two bytes, available since PHP 5.3
{% endhighlight %}

The following code prints the values of pow(3,n), n<10, starting with n=0:

{% highlight php %}
<?for(;$n++<9;)echo$a=3*$a?:1,"\n";
{% endhighlight %}

If you do have only an if (and no else), try to negate the condition. Since the middle part of the of ternary operator might be left out.

So:
{% highlight php %}
<?if($a==$b)doSomething();
{% endhighlight %}

Equals (Since PHP 5.3>):
{% highlight php %}
<?$a!=$b?:doSomething();
{% endhighlight %}

Even equals:
{% highlight php %}
<?$a!=$b||doSomething();
{% endhighlight %}

Since the associativity of this operator is left, nested ternary-operators should be preferable done in the true-action, since you otherwise have to use parentheses.
{% highlight php %}
<?$a=2;$b=3;print$a==$b?$a==27?$b!=30?:'This situation will never happen':'':'';
{% endhighlight %}

Same code, but false-based:
{% highlight php %}
<?$a=2;$b=3;print$a!=$b?:($a!=27?'':$b!=30)?'':'This situation will never happen';
{% endhighlight %}

Compare the examples below that both print all primes below 1000.
{% highlight php %}
1<?for($a=array(),$b=1;++$b<=1000;){foreach($a as$c)if($b%$c==0)continue 2;$a[]=$b;echo"\n".$b;}
{% endhighlight %}

{% highlight php %}
1<?for($a=array(),$b=1;++$b<=1000;){foreach($a as$c)continue($b%$c?0:2);$a[]=$b;echo"\n".$b;}
{% endhighlight %}

The whole if-structure can here be replaced with a ternary operator. Also try to avoid the need of keywords like 'break' and 'continue', since they need a lot of bytes, while it even might be done using a variable, that perhaps even might be used for other purposes.

Rewritten without *if* and *continue*, although this is far from the optimal solution:
{% highlight php %}
1<?for($a=array(),$b=1;$d=++$b<=1000;){foreach($a as$c)$b%$c?:$d=0;if($d){$a[]=$b;echo"\n".$b;}}
{% endhighlight %}

## Functions

You should (almost) never write your own functions. In most cases it is unnecessary and it costs a lot of bytes.
Some built-in functions in PHP should never be used. These are some examples with a better equivalent to the right.
    
* <a href="http://php.net/rtrim">rtrim</a> -> <a href="http://php.net/chop">chop</a>
* <a href="http://php.net/explode">explode</a> -> <a href="http://php.net/split">split</a>
* <a href="http://php.net/implode">implode</a> -> <a href="http://php.net/join">join</a>
* <a href="http://php.net/preg_split">preg_split</a> -> Could use split() instead in most cases
* <a href="http://php.net/preg_replace">preg_replace</a> -> <a href="http://php.net/preg_filter">preg_filter</a> is one byte shorter,and in most cases exactly the same.
* <a href="http://php.net/print">print</a> -> <a href="http://php.net/echo">echo</a>


In some cases, print might be useful since it can be used as a function while echo can't:
{% highlight php %}
<?for(;++$i<11;){echo str_repeat(' ',10-$i);for($a=0;$a<$i;)echo$a,(++$a-$i?' ':"\n");}
{% endhighlight %}

{% highlight php %}
<?for(;++$i<11;print"\n")for(print str_pad($a=0,11-$i,' ',0);++$a<$i;)echo" $a"; # echo instead of print would give an error
{% endhighlight %}

* <a href="http://php.net/lcfirst">lcfirst</a> -> `$a|' '` or `$a|~ß`
* <a href="http://php.net/strtoupper">strtoupper($a)</a> -> if $a is only one char, you can use: `$a&ß` or `$a&'ß'`
* <a href="http://php.net/ucfirst">ucfirst</a>: see strtoupper
* echo <a href="http://php.net/sprintf">sprintf(...)</a> -> <a href="http://php.net/printf">printf(...)</a>
* <a href="http://php.net/str_replace">str_replace</a> -> consider whether <a href="http://php.net/strtr">strtr</a> can be used
* <a href="http://php.net/array_unique">array_unique</a> -> Most times this function is useless. If something has to be unique, you can do it in many different ways dependent on context.


All three examples below shows all unique letters in the string in capitals:
{% highlight php %}
<?$b=array_unique(str_split(strtoupper('This is a string')));sort($b);if($b[0]==' ')unset($b[0]);echo join($b,"\n");
{% endhighlight %}

{% highlight php %}
<?for($a='This is a string';$b=$a[$i++];sort($c))@in_array($b&=ß,$c)?:$b==' '?:$c[]=$b;echo join($c,"\n");
{% endhighlight %}

{% highlight php %}
<?for($a=count_chars(strtoupper('This is a string'),3);$c=$a[$b++];)$c==' '?:$d[]=$c;echo join($d,"\n");
{% endhighlight %}

{% highlight php %}
<?for($a='This is a string';$b=$a[$i++];)$b==' '?:$c[$b&=ß]=$b;sort($c);echo join($c,"\n");
{% endhighlight %}

* <a href="http://php.net/sizeof">sizeof</a> -> most times unnecessary, if needed use <a href="http://php.net/count">count</a>.
* <a href="http://php.net/count">count</a> -> most times unnecessary. See examples below:

{% highlight php %}
<?for($a=array(5,24,89);$i<count($a);)echo$a[+$i++],"\n";
{% endhighlight %}

{% highlight php %}
<?for($a=array(5,24,89);$b=$a[+$i++];)echo"$b\n";
{% endhighlight %}


* <a href="http://php.net/floor">floor</a> -> Often can done with (int)$a (If you only want to do echo floor($a); you might consider printf('%u',$a);)
* <a href="http://php.net/preg_replace">preg_replace</a> and <a href="http://php.net/preg_filter">preg_filter</a> does have an e-flag, which means that the replacement is being executed as shown in the example below.


Both solutions below are written by JWvdVeer for the <a href="http://stackoverflow.com/questions/3190914/code-golf-pig-latin">PIG-latin golf challenge</a>:
{% highlight php %}
<?foreach(split(~ß,SENTENCE)as$a)echo($b++?~ß:'').(strpos(' aeuio',$a[0])?$a.w:substr($a,1).$a[0]).ay;
{% endhighlight %}

{% highlight php %}
<?=preg_filter('#b(([aioue]w*)|(w)(w*))b#ie','"$2"?"$2way":"$4$3ay"',SENTENCE);
{% endhighlight %}

The second solution is much shorter than the first. It even can handle strings with punctuation.


## Operators

### Precedence
Know the precedence of operators. A table with information about the precedence can be found at: <a href="http://php.net/manual/language.operators.precedence.php">http://php.net/manual/language.operators.precedence.php</a>. This is important. Because then you know when (not) to put parentheses around your piece of code.
Try to concatenate operators as often as possible. Set the variables $a, $b, $c to 1, $d to 'None' and $e has to be incremented? Then it should be:

{% highlight php %}
<?condition?$e+=$a=$b=$c=1|$d=None:0;
{% endhighlight %}

Not:
{% highlight php %}
<?if(condition){++$e;$a=$b=$c=1;$d=None;}
{% endhighlight %}

Or incremented $b with $c, then added to $a, and showing whether $a is odd or even after that increment?
{% highlight php %}
<?echo'$a is ',(1&$a+=$b+=$c)?odd:even;
{% endhighlight %}

### Modulo operator (%)

Modulo is a really useful operator for doing actions that only have to be done once in so many times in a loop or with some given condition. The condition to the loop can be a variable you can use for this purpose.

So if something has to be done every each 9th iteration:
{% highlight php %}
<?for(;$i<100;)++$i%9?:doSomething();>
{% endhighlight %}

It is comparable to the bitwise &-operator in the cases that if a%b is given, b is a power of two. In that case it is exactly the same as a&(b-1). So $a%8 is exactly the same as $a&7. Only the precedence of these two operators is different. So use the right one in your context.


## Assignment Operators

If possible always try to use +=, -=, %=, &=, |=, etc.
Mind the fact is associativity is right. So first the most right assignment operator in the expression will be executed.

Which makes this:
{% highlight php %}
<?$a+=$b%=2;
{% endhighlight %}

exactly the same as:
{% highlight php %}
<?$b%=2;$a+=$b;
{% endhighlight %}

But not the same as:
{% highlight php %}
<?$a+=$b;$b%=2;
{% endhighlight %}

## Bitwise

### Bitwise XOR (^)
Bitwise XOR for integers is a replacement for !=

Numeric example:
{% highlight php %}
<?$i!=7?:print'$i is seven';
{% endhighlight %}

Equals:
{% highlight php %}
<?$i^7?:print'$i is seven';
{% endhighlight %}

On strings it might be very useful to determine whether the given character equals a given char. This can be done by XOR the given char to '0', since '0' evaluates false.

Example check whether char equals '_':
{% highlight php %}
<?$c=_;echo'Char is '.($c^o?'not ':'').'an underscore';
{% endhighlight %}
This trick only can be used on one char. Since '0' evaluates false, but '00...' evaluates true.

### Bitwise OR (|)
Used for several purposes. One of them is converting letters to lowercase (see strtolower and lcfirst in section *functions*).
Mind the fact that $int|$nonNumericString==$int==true. Sometimes this might be useful, because you don't need a semicolon instead and your code might be written in one expression (for example in a ternary-operator).

### Bitwise NOT (~)
Covered in the *String* section.
