```c
struct sigaction old_handler;
sigaction(signum, NULL, &old_handler);
return old_handler.sa_handler == SIG_IGN;
```
