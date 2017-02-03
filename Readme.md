To compile under MinGW
======================
Type:
   gcc Parent.c -o Parent.exe
   gcc Child.c -o Child.exe
   
To run
======
Type:
   ./Parent.exe HelloMsg.txt
   
Note
====
I modified the original code [1]. In its original form, the Parent code did
not terminate when the Child program terminated. This was because the
ReadFile(.) blocks till the child wrote to STDOUT. But the child had already
terminated. I attempted to use asynchronous ReadFile(.) using OVERLAPPED to
timeout if the child stopped writing for sometime (because it terminated) as
suggested here [2]. However, it did not seem to work. It was still blocking on
ReadFile(.). I am not sure why and I am not sure if it is possible to solve
the way I did [3]. 

StringCchPrintf(.) is not available under MinGW. So I replaced it with
snprintf(.) as suggested here [4].
   
   
References:
[1]  https://msdn.microsoft.com/en-us/library/windows/desktop/ms682499(v=vs.85).aspx
[2]  http://stackoverflow.com/questions/7955199/device-driver-windows-readfile-function-timeout
[3]  http://stackoverflow.com/questions/13767989/named-pipe-reading-timeout
[4]  http://betterlogic.com/roger/2010/08/undefined-reference-to-stringcchprintf/