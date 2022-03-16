# How To Basic: Shell Scripting

This repo is mainly used by me for notetaking as I learn the basics of Shell scripting. It is publically available in case anyone finds it, since it can be handy for those who already have a basic foundation in programming.

## Basics
Using a wildcard or file extensions in the `echo` command will work similarly as `ls` or any other fs command.
i.e.
```sh
echo *      # Prints all the files in the current directory
echo *.html # prints the names of all the files in the current directory
```

Unlike C, denoting a hex (0x...), binary (b...), or octal (o...) output in echo will not warrant the output as that base of the number, rather it will print as a string literal.

The symbols `$` and `"` are processed by the shell even when placed inside double quotes. Using the escape character `\` will prevent this from happening. This also includes the escape character itself.

`$` denotes accessing a variable
variable declaration: `MY_VAR="some value of any type"`
There is no spacing around the equal sign, otherwise it will be interpreted as an argument to a non-existent function of `MY_VAR`

## Control Flow
For loop syntax:
```sh
for $counter in 1 2 3 4... {range}
do
	#loop body
done
```

While loop syntax:
```sh
WHILE_CONDITIONAL=true
while ["$WHILE_CONDITIONAL" != "false"]
do
	#while body
done
```

Using a colon `:` in place of an exit condition in the loop will render it an indefinite loop (`while(true)` in C, Java, and other languages)

## Conditional Flow
### Test/If
`[]` is symbolic for the keyword `test` which is used for conditionals
i.e.
```sh
if [...]
then
# if-body
else
# else-body
fi
```
or
```sh
if[...]; then
#if-body
	elif[...]; then
	#elif-body
	else
	#else-body
fi
```

__note: the end of an if statement is noted with `fi`, the same applies for case statements (`esac`)__
__note: semicolons are used to join multiple lines__
__note: backslashes are used to split single-line commands across two lines__
i.e.
```sh
if["$X" -nt "/etc/passwd"] && \ echo "X is a file which is newer than /etc/passwd"
```

Simpler way of writing if statements:
`&&` and `||` commands give code to run if the result is true, or false respectively (similar to a ternary op)
i.e.
```sh
[$X -ne 0] && echo "X isn't zero" || echo "X is zero"
```
### Case
```sh
case $INPUT in hello)
	echo "Hello world!"
	;;
	bye)
	echo "see you later!"
	break #use `exit` if we want to exit the script completely
	;;
	*)
	echo "what?"
	;;
esac
```
## Read-only variables
```sh
$0      #basename of the program
$1...$9 #the first 9 parameters the script was called with
$@      #all passed parameters
$*      #all passed parameters as individual arguments
$#      #number of passed parameters
$?      #exit value of the last run command
$$.     #Process IDentifier (PID) of the currently running shell
$IFS    #Internal Field Separator (default: SPACE TAB NEWLINE)
```
### More on $*
If we pass a file to our script with spaces (i.e. "my text file.txt"), $* will output it as "my""text""file.txt"
Therefore $* should be avoided when possible
### More on \$\$
Can be used for creating temporary files such as
`/tmp/my-script.$$` which is useful if many instances of the script could be run at the same time, and they all need their own temporary files
### More on $!
This is useful to keep track of the process as it gets on with its job
## More on Variables
Curly brackets are handy
i.e.
```sh
foo = sun
echo $fooshine    #$fooshine is undefined
echo ${foo}shine  #displays "sunshine"
```

`:-` can be used to define a default value for a variable if one is not assigned
i.e.
```sh
echo -en "what is your name [`whoami`]"
read myname
echo "your name is : ${myname:-`whoami`}"
```
In this case, `whoami` will return your login name (UID). \`whoami\` will run in a subshell, meaning that any commands run in the backticks will not affect the currently-running shell
`:=` sets the variable to the default if it is undefined
## External Programs
### Backticks
The backtick is used to indicate that the enclosed text is to be executed as a command
i.e.
```sh
MYNAME=`grep "^${USER}:" /etc/passwd | cut -d: -f5`
echo $MYNAME
```
__Note: for slower commands such as `find`, it may be best to run it once, store it in a variable, then use other commands such as `grep` or `echo` on the variable to preserve execution time__

## Functions
A function may return a value in one of four ways:
	1) Change the state of (a) variable(s)
	2) Use the `exit` command to end the shell script
	3) Use the `return` command to end the function, and return a value to the calling section of the shell script
	4) `echo` output to `stdout`, which will be caught by the caller just as c=`expr $a+$b` is caught
A shell function cannot change its parameters, though it can change global parameters

Simple script example:
```sh
#!/bin/sh

add_a_user()
{
	USER=$1
	PASSWORD=$2
	shift; shift; #shift allows us to have more parameters
				  #having shifted twice, the rest is now comments
	COMMENTS=$@
	echo "Adding user $USER ..."
	echo useradd -c "$COMMENTS" $USER
	echo passwd $USER $PASSWORD
	echo "Added $USER ($COMMENTS) with pass $PASSWORD"
}

##main body of the script
echo "Start of script..."
add_a_user bob letmein Bob Holness the presenter
add_a_user fred baspassword Fred Durst the singer
add_a_user bilko worsepassword Sgt. Bilko the role model
echo "End of script..."
```

Parentheses are used as a function declaration followed by curly braces (similar to functions/methods in other programming languages)
Similar to JS, function declaration and usage are not necessarily sequential. However, when we use `shift`, we will lose the original value of `$1...`  within the function, so it is a good idea to store it to a variable.

For shell functions, there is no variable scoping other than $1..., $@, etc. Additionally, we cannot change the parameters of arguments, but we can change their original variable values (call-by-value in essence)
i.e.
```sh
#!/bin/sh
myfunc()
{
echo "\$1 is $1"
echo "\$2 is $2"
a="Goodbye Cruel"
}

a=hello
b=world
myfun $a $b
echo "a is $a"
echo "b is $b"
```
Output:
```
$1 is hello
$2 is world
a is Goodbye Cruel
b is world
```


## Recursion
`factorial.sh`
```sh
factorial()
{
if ["$1" -gt "1"]; then
	i=`expr $1 - 1`
	j=`factorial $i`
	k=`expr $1 \* $j`
	echo $k
else
	echo 1
fi
}

while :
do
	echo "Enter a number:"
	read x
	factorial $x
done
```

## External Libraries
\#TBD
