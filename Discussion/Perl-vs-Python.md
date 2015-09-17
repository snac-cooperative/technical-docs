A comparison of Perl 5 and Python 3.

Tom's fundamental conclusion (still being debated) is that Perl5 wins based on stability, size of CPAN, and
useful everyday programming features. Python loses due to application-breaking changes in even minor version
updates **(needs citation, since research shows only the major version has caused breaking changes)**, has fewer modules, and lacks language features (syntactic sugar especially) to support common idioms.

Perl6 is a wildcard in this comparison. If Perl6 ships in 2015 as scheduled (when exactly? December?) it
appears that it will exceed Python in terms of language features, and it will have the CPAN advantage as
well. It also retains the good features of the original Perl 5.

## Python 3

Python pros:

- many powerful language features
- arguably more modern than perl 5
- good library of modules
- py-lint lint checking helps analyze common issues with code
- works with Apache httpd native (via shebang)
- used by smart programmers, hipster approved
- out-of-the box utf8 support
- native tuple data type (need example, if Perl has this, probably requires a module)

- printf() is trivially defined (related to string interpolation and .format() discussion below)

```
#!/usr/bin/python
import os
import re
import sys
def printf(format, *args):
    sys.stdout.write(format % args)

if __name__ == '__main__':
    var = 1234
    hash = {'foo' : 5678}
    print "var:" , var, '\n'
    print "hash:", hash['foo'], "\n"
    printf("var: %4.4s hash: %10.1s\n", var, hash['foo'])

> ./printf.py                        
var: 1234 

hash: 5678 

var: 1234 hash:          5
```


Python cons:

- no increment via ++ use += instead. http://stackoverflow.com/questions/2632677/python-integer-incrementing-with

- no syntax checker, but there is  py-lint

- lack of syntax for some common idioms. Assignment success test in while() is not valid syntax:
  `while ($foo = funct()) <code block>` **Are you sure?**
  
  ** Tom: what is wrong with the syntax below? Checking the implicit boolean value of an assignment is common in some
  languages, I think. Perl does it and I thought PHP, and others? **

```
#!/usr/bin/python
import os
import re
def read_state_data(file, cols = []):
    print 'file: ', file
    fp = open(file, "r")
    while (temp=fp.readline()):
        print 't: ', temp

if __name__ == '__main__':
    print 'Running'
    read_state_data('states_v2.dat', ['order', 'edge', 'choice', 'test', 'func', 'next'] )

> ./while_demo.py 
  File "./while_demo.py", line 18
    while (temp = fp.readline()):
                ^
SyntaxError: invalid syntax
```
- see printf() in the pros section above

- lack of string interpolation **Python has this, [doc](https://docs.python.org/3/library/string.html#format-string-syntax)**

**Tom: strings are not interpolated. Some alternative must be used. See the Perl (one-liner) interpolation below.** 

```
#!/usr/bin/python
import os
import re
if __name__ == '__main__':
    var = 1234
    hash = {'foo' : 5678}
    print "var:" , var, '\n'
    print "hash:", hash['foo'], "\n"
    print "non-interpolated var: var hash: hash['foo']\n"

> ./interpolation_demo.py
var: 1234 

hash: 5678 

non-interpolated var: var hash: hash['foo']
```

**Tom: Format functions are not string interpolation. Perl does this: "value of var: $var\nvalue of hash: $hash{foo}\nDone.\n"**


```
> perl -e ' $var=1234; $hash{foo}=5678; print "value of var: $var\nvalue of hash: $hash{foo}\nDone.\n";'
value of var: 1234
value of hash: 5678
Done.
```

**Tom: Python apparently can't do-what-i-mean (dwim) and type cast ints to strings in order to print them? That's
ugly. Surely there is a Pythonic idiom to get around this.**

```
#!/usr/bin/python
import os
import re
if __name__ == '__main__':
    var = 1234
    hash = [5678]
    print "var:" + var + '\n'
    print "hash:" + hash['foo'] + "\n"
    print "interpolated var: var hash: hash['foo']\n"

> ./interpolation_demo.py
Traceback (most recent call last):
  File "./interpolation_demo.py", line 7, in <module>
    print "var:" + var + '\n'
TypeError: cannot concatenate 'str' and 'int' objects
```

The answer is to use comma to concatenate, or format(): http://stackoverflow.com/questions/11559062/concatenating-string-and-integer-in-python



**Tom: This is a bug, not a feature. As the OP notes "Even Java does not need explicit casting to String to do
this sort of concatenation. " I wouldn't call it a show stopper, but it is a extra typing to do something that
happens all the time. Similar and perhaps more standard are printf() and sprintf() Must check if Python has
printf/sprintf.**

- lack of regular expression binding operator (=~) means that Python is limited to a functional use of regular
  expressions which is awkward compared to Perl [Python's regex](https://docs.python.org/3/howto/regex.html)

**Tom: Can Python do a regex substitution, consuming the string and matching one or more groups? I can't find group() as related to sub(), but only to match(). Substitution while consuming all or part of the original string is a common idiom, as seen below in a Perl example. I've been reading Python docs for 30 minutes and can't figure out how to do grouping with sub().**
  
```
#!/usr/bin/perl

$var = 'abcd';

while($var =~ s/^(.)//g)
{
    print "trimmed off: $1\n";
}

  > ./regex.pl 
trimmed off: a
trimmed off: b
trimmed off: c
trimmed off: d
```

Or as a oneliner:

```
> perl -e '$var='abcd'; while($var =~ s/^(.)//g) { print "trimmed off: $1\n"; }'
trimmed off: a
trimmed off: b
trimmed off: c
trimmed off: d
```

- lack of syntactic sugar for dictionaries in that the key must be quoted.  **Robbie: Python handles dicts/arrays very nicely**

**Tom: Perl is happy with $hash{key} and $hash{'key'} and only requires the quote when the key literal is ambiguous. All the non-Perl languages I know always require quotes around literal keys. Typing the perl sigil ($, @, %) is extra typing, but many programmers do more typing than that in variable nameing: str_var, int_var, etc. just to keep track of variable types which are obscure without the sigil.**

- whitespace is significant; yes, Pythonistas consider it a feature, but:

 - anywhere except Python source code, significant whitespace leads to bugs (Rhetorically: why doesn't it lead to bugs in Python?)

 - the thousandth time you have to hit the tab key multiple times to get a new line properly indented you
   may start to wish for curly brackets. **Tom: must try Emacs indent-region which may partially overcome this.**
   
 - whitespace not allowed, or simply not Pythonic style? From py-lint: **I believe this is py-lint enforcing a particular coding style, as this is valid python**
```
C: 44, 0: No space allowed before bracket
    read_state_data('states_v2.dat', ['order', 'edge', 'choice', 'test', 'func', 'next'] )
```

- smaller number of modules than in CPAN

- functions have to be defined in the file above their call (or presumably pre-declared) which is awkward and
  like ancient history of C. **Do you count included in the file, but defined elsewhere, such as PHP, C++, and Java?**

- Python oneliners are somewhat more verbose than Perl. 

- RHEL doesn't have Python 3 in the standard package (yum) repository.

http://stackoverflow.com/questions/8087184/installing-python3-on-rhel

```
sudo yum install http://dl.iuscommunity.org/pub/ius/stable/CentOS/6/x86_64/ius-release-1.0-11.ius.centos6.noarch.rpm
sudo yum search python3

# List all into a file instead of searching, allows less and grep later:
sudo yum list all > yum_list.txt

sudo yum install python33
```


Misconceptions of Python

- Python 3 broke compatibility with Python 2, for obvious reasons (there were serious issues in Python 2).  They have been rectified in Python 3, and new minor versions include new features that do not break old ones.

**Tom: Ok. So v2 broke with minor versions, which explains why there are 3 or 4 sub-versions of v2 by default with MacOS.**


## Perl 5

Perl pros:

- syntactic sugar and DWIM make everyday tasks pleasant, code is expressive but not verbose.

```
# regex binding operator
$var = "The quick brown";
if ($var =~ m/quick/)
{
    print "found quick\n";
}

# literal hash keys optional quoting
my %hash;
$hash{literal} = 1;
$hash{bar} = 2;
$hash{'literal with space needs quoting'} = 3;

# variable casting implicit (and an example of string interpolation)

# $var is a number, but when tested against a regex, Perl simply casts to a string because that is clearly
# what the programmer wanted.

$var = 1234;
if ($var =~ m/(23)/)
{
    print "matched string \"$1\" in number: $var\n";
}

```

- huge library (149,563 modules) in CPAN

- string interpolation, which is lacking in all (nearly?) all other languages. This feature is a direct result
  of Perl's use of sigils on variable names (and optionally on functions).

- the best regex syntax

- foreach loop iterator change in place

- stable, updates to perl won't break applications, active development fixes bugs and even adds the occasional new feature

- runs under Apache httpd native, via shebang

- available Apache mod_perl for improved performance with Apache httpd (not that we will ever need this).

- good CGI support, and CGI scripts also run unchanged at the command line, and don't break pipes or other
  shell features. This is very useful for debugging CGI.

- excellent database modules in DBI and DBD

- lexical scoping (and dynamic scoping, apparently), allows local declaractions for side-effect free code

- perl -cw syntax checking, as well as Perl::Critic, B::Lint, and several others (once again CPAN saves the
  day with a huge number of modules)

- Moose is a postmodern object system for Perl 5 that takes the tedium out of writing object-oriented Perl. It
  borrows all the best features from Perl 6, CLOS (Lisp), Smalltalk, Java, BETA, OCaml, Ruby and more, while
  still keeping true to its Perl 5 roots. Moose is 100% production ready and in heavy use in a number of
  systems and growing every day. Get Moose from CPAN. Also see: http://moose.iinteractive.com/en/




Perl cons:

- arguably a clumsy OOP implementation (overcome by Moose?)

- lots of bad things programmers can do, but should avoid

- hipsters view perl 5 as something only old folks do; a dinosaur

- subroutine parameter passing only via a flat list is awkward (of course the list can include references, and due to
  Perl dwim, a hash is a list which allows for named parameters). Pass by reference avoids the list flattening issues.
  
- subroutine returns are also a flat list, the same issues/constraints/features apply as with subroutine parameters.

- references can be awkward, and often the simplist solution is to look up a common idiom.

- um...
