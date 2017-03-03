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
not terminate when the child process terminated. The parent code blocked at
`ReadFile(.)` till the child wrote to `STDOUT`. When the child process
terminated, the parent was not informed and the latter blocked forever on the
`ReadFile(.)`.

I modified the code to wait for the child process to signal its termination
using `WaitForSingleObject(childProcInfo->hProcess, 0)`. The value 0 means the
function does not wait, but returns immediately whether the object has signaled
or not. This is to prevent blocking, so that the parent can continue its
reading and printing. This idea came to me after reading [5].

I previously attempted using asynchronous `ReadFile(.)` using `OVERLAPPED` to
timeout if the child stopped writing for sometime (because it terminated) as
suggested here [2]. However, it did not seem to work. It was still blocking on
`ReadFile(.)`. I guess it is because `OVERLAPPED` only works for serial comm.
like UART, but not stdin/stdout. See commit: 14352e24b5d39606 if you are
interested to look at the code.

`StringCchPrintf(.)` is not available under MinGW. So I replaced it with
`snprintf(.)` as suggested here [4].

References
==========
1.  https://msdn.microsoft.com/en-us/library/windows/desktop/ms682499(v=vs.85).aspx

2.  http://stackoverflow.com/questions/7955199/device-driver-windows-readfile-function-timeout

3.  https://blogs.msdn.microsoft.com/oldnewthing/20110707-00/?p=10223

4.  http://betterlogic.com/roger/2010/08/undefined-reference-to-stringcchprintf/

5.  http://www.catch22.net/tuts/undocumented-createprocess
