---
title: "Tcl Basics"
date: 2018-07-15T22:49:20+05:30
Description: ""
Tags: ["TCL"]
Categories: ["Programming"]
---

# Introduction

TCL commands' general syntax is:

```tcl
cmd arg1 arg2 ... ;
```

where cmd is any standard TCL command or user defined procedure. semicolon(;) is optional unless needed.

For any command, you may type command name and press enter on TCL shell, TCL shows its required arguments. Parameters showing between questions marks(??) are optional e.g. puts:

```tcl
% puts
wrong # args: should be "puts ?-nonewline? ?channelId? string"
```

Here -nonewline and channelId are optional and carry their default (channelId is stdout) values.


# Printing to stdout

Command ```puts``` prints to stdout by default:

```tcl
puts "Hello World!"
```

# Comments

Lines starting with hash (#) are considered as comments:

```tcl
# This line is a comment
```

For inline comment, the statement should be terminated with semicolon first:

```tcl
puts "some string"              ;# an inline comment, note the semicolon
```

# Variables and Expressions

Variables are defined using ```set``` command. They can be dereferenced either with $varname or by set command:

```tcl
set foo 1                       ;# defining variable
set foo_val $foo                ;# getting its value with $
set foo_val [set foo]           ;# getting value with set
```

Note the use of brackets []. TCL evaluates anything between [], which is further substituted with the result produced. There is an exception, if [] is inside braces i.e. {[]}, it doesn't get evaluated.

```tcl
set foo_val {[set foo]}         ;# foo_val will contain [set foo]
```

...but hey! there is ```eval``` command which interprets its arguments first and then the whole statement is reinterpreted:

```tcl
eval set foo_val {[set foo]}    ;# foo_val now contains 1 as it first becomes set foo_val [set foo]
```

Double quotes ("") and braces({}) both are used for grouping. The difference between the two is that words inside double quotes are evaluated but not when inside braces:

```tcl
set foo 1
puts "foo = $foo"               ;# will output foo = 1
puts {foo = $foo}               ;# will output foo = $foo
```

> Question: eval on puts {foo = $foo} will throw error. Do you know why?

The ```expr``` program is used to perform arithmetic:

```tcl
set foo [expr {2.0**3 + 5/5 - 2*3}] ;# 3.0
expr {int($foo)}                    ;# 3
expr {22/7}                         ;# 3
expr {22.0/7}                       ;# 3.142857142857143
```

> Always enclose expression for expr with braces {}. See explanation [here](http://wiki.tcl.tk/10225)

We saw how to print string only with puts. ```format``` command is used to print a formatted output

```tcl
puts [format "int:%d hex:%#010x float:%3.1f" 12321 12321 1.2321] ;# int:12321 hex:0x00003021 float:1.2
puts [format {int:%d hex:%#010x float:%3.1f} 12321 12321 1.2321] ;# int:12321 hex:0x00003021 float:1.2
```

Note that "" and {}, both doing the job for format. This is because we just needed grouping here, no evaluation.

# Procedures - Functions

Procedures(Functions) are defined with ```proc``` command. Please note that proc (and other constructs which require braces for body) requires opening brace for body i.e. { on the same line as the command. So, there is only one correct way to do it!

```tcl
# definition
proc fact {n} {

    if {$n == 1} {
        return 1
    }
    return [expr {$n * [fact [expr {$n-1}]]}]
}

# call fact now
fact 5                          ;# 120
```

Did you notice? After defining proc, it is used like any other standard TCL command!

Default values for parameters is defined with the help of extra braces {}.

```tcl
proc print {str {indent 4}} {

    puts [format "%s%s" [string repeat " " $indent] $str]
}
print "with default indentation"    ;#    with default indentation
print "with 8 spaces indentation" 8 ;#        with 8 spaces indentation
```

> Only trailing parameters (yes, multiple) can be optional. Definition with an optional parameter and then a mandatory parameter, will not make first parameter optional.

# Control flow

Control flow is supported with ```if-elseif-else```, ```switch```, ```for```, ```foreach```, ```while```, ```break``` and ```continue``` constructs.

The expression inside {} for if, for and while statements is already evaluated by expr program, so no need to write it explicitly. Let's go through one example of each:

## if-elseif-else

```tcl
if {!($num % 2)} {
    puts "num: $num is divisible by 2"
} elseif {!($num % 3)} {
    puts "num: $num is divisible by 3"
} else {
    puts "num: $num is not divisible by 2 or 3"
}
```

## switch

Syntax:

```tcl
switch ?options? -- "some_string" {     ;# -- denotes end of options for switch so that some_string can contain - and not be treated as an option to switch
    case0 {
        statements
    }
    .
    .
    caseN {
        statements
    }
    default {
        default action or error handle
    }
}
```

Example:

```tcl
set i 3
switch -- [expr {$i % 2}] {
    0 {
        puts "$i is even"
    }
    1 {
        puts "$i is odd"
    }
    default {
        error "Bad Exception!"
    }
}
# 3 is odd
```

## while

```tcl
set i 10
while {1} {                     ;# run forever
    if {$i < 1} {
        break                   ;# exits loop if i is less than 1
    }
    if {$i % 2} {
        incr i -1
        continue                ;# continue for next iteration from here if i is odd
    }
    puts -nonewline "$i "       ;# prints i if even
    incr i -1                   ;# decrements i
}
# 10 8 6 4 2
```

## for

General syntax:

```tcl
for {initialization} {run if expression is true} {execute on each iteration end} {
}
```

Example:

```tcl
for {set i 0} {$i < 3} {incr i} {
    puts -nonewline "$i "
}

# 1 2 3
```

## foreach

```tcl
foreach i {1 2 3} {
    puts -nonewline $i
}
# 123
```

# Data Structures

We will look at three data structures now: string, list and array.

## String

A string is initialized with double quotes (""):

```tcl
set foo_str "this is a string"
```

You may perform operations on strings with ```string``` command:

```tcl
string length $foo_str          ;# returns total no. of characters in string. Here, 16
string reverse $foo_str         ;# returns reversed string. Here, gnirts a si siht
string range $foo_str 0 end     ;# returns a sub string. end refers to last char in string. Here, full string
```

## List

List is like an array in C. Initialize a list variable as

```tcl
set foo_list {item1 item2 item3}
```

or as follows which allows to put inline comments or some variable which is not directly possible with above syntax.

```tcl
set foo_list [list]
lappend foo_list "item1"        ;# some comment
lappend foo_list "item2"
lappend foo_list "item3"
```

Iterating over a list:

```tcl
foreach item $foo_list {
    puts $item
}
```

or:

```tcl
for {set i 0} {$i < [llength $foo_list]} {incr i} {
    puts "$i: [lindex $foo_list $i]"
}
# 0: item1
# 1: item2
# 2: item3
```

## Array

Array is like hash which carries key-value pair for its items. An array is initialized with array set command:

```tcl
array set foo_arr {a 97 b 98 2 4 3 9}
```

Dereferencing array elements:

```tcl
array get foo_arr               ;# a 97 2 4 b 98 3 9
puts $foo_arr(a)                ;# 97
```

Iterate over array elements:

```tcl
foreach {k v} [array get foo_arr] {
    puts "key: $k, value: $v"
}
# key: a, value: 97
# key: 2, value: 4
# key: b, value: 98
# key: 3, value: 9
```

# File handling

The ```open``` and ```close``` commands are used to open and close files respectively. open returns a channel no.(file descriptor) to work with.

Writing:

```tcl
set fd [open "gv.txt" w]
puts $fd "line 1"
puts $fd "line 2"
close $fd
```

Reading:

```tcl
set fd [open "gv.txt" r]
while {[gets $fd rd_line] >= 0} {
    puts $rd_line
}
close $fd
```

Three channels i.e. stdin, stdout and stderr are opened by default. You may use these directly.

```tcl
puts stderr "some warning or error" ;# prints message on stderr
set foo [gets stdin]                ;# prompts user to input value for foo
```

# Exceptions

For robustness of our code, we should catch any possible exceptions and work accordingly. ```catch``` command helps in catching exceptions:

```tcl
if {[catch {expr {1/0}} err_msg]} {
    error "error in expression: $err_msg"
}
# error in expression: divide by zero
```

Note that catch returns 0 if there is no exception, else 1. Corresponding error message is captured in err_msg. ```error``` command reports the error on stderr and /aborts/ the execution.


# Running a script

TCL scripts are regular text files and generally carry =.tcl= extension. ```source``` command helps evaluates a script in a running TCL interpreter. So, you can have your custom procs in one file and source it in your main file:

file myprocs.tcl:

```tcl
proc fact {n} {

    if {$n == 1} {
        return 1
    }
    return [expr {$n * [fact [expr {$n-1}]]}]
}
```

file main.tcl: argc variable contains the no. of commandline arguments passed and argv list variable contains the arguments.

```tcl
#!/usr/bin/env tclsh

# argument checking
if {$argc < 1} {
    error "pass an integer"
} elseif {$argc > 1} {
    error "too many arguments, pass just one integer"
} else {
    # check if argument is integer or not
    set num [lindex $argv 0]
    if {![string is integer $num]} {
        error "$num is not an integer"
    }
}

source ./myprocs.tcl

puts "factorial of $num: [fact $num]"
```

Running script on bash shell:

```tcl
> ls
main.tcl  myprocs.tcl
> chmod u+x main.tcl
> ./main.tcl 5
factorial of 5: 120
```

This is just basic introduction to TCL but I hope it will get you started! Thanks!
