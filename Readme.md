What is it?
===========
This is an example program to demonstrate how to communicate with a child process
through pipe. The child perfoms input/output as usual using stdin/stdout like
`scanf(.)` and `printf(.)`. Pipe technique redirects the child's stdin/stdout to
the in/out streams created by the parent program.

To compile under MinGW
======================
Type:
```
gcc Parent.c -o Parent.exe
gcc Child.c -o Child.exe
```
   
To run
======
Type:
```
./Parent.exe HelloMsg.txt
```
   
Note
====
I modified the original code [1]. In its original form, the Parent code did
not terminate when the child program terminated. This was because the
`ReadFile(.)` blocks till the child wrote to `STDOUT`. But the child had already
terminated. I attempted to use asynchronous `ReadFile(.)` using `OVERLAPPED` to
timeout if the child stopped writing for sometime (because it terminated) as
suggested here [2]. However, it did not seem to work. It was still blocking on
`ReadFile(.)`. I am not sure why and I am not sure if it is possible to solve
the way I did [3]. 

A possible solution is to use `WaitForSingleObject(piProcInfo.hProcess, 0)` to
determine if the child process has terminated. The value 0 means the function
does not wait, but returns immediately whether the object has signaled or not.
See first example in [5] on how to wait until the child terminates using `INFINTE`.
I have not tried this idea yet.

`StringCchPrintf(.)` is not available under MinGW. So I replaced it with
`snprintf(.)` as suggested here [4].
   
References
==========
1.  https://msdn.microsoft.com/en-us/library/windows/desktop/ms682499(v=vs.85).aspx

2.  http://stackoverflow.com/questions/7955199/device-driver-windows-readfile-function-timeout

3.  http://stackoverflow.com/questions/13767989/named-pipe-reading-timeout

4.  http://betterlogic.com/roger/2010/08/undefined-reference-to-stringcchprintf/

5.  http://www.catch22.net/tuts/undocumented-createprocess
