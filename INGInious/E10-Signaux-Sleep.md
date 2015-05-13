```c
sigjmp_buf env;

static void sig_handler(int signal) {
    siglongjmp(env, 1);
}
```

```c
signal(SIGALRM, sig_handler);

if (sigsetjmp(env, 1) == 0) {
    alarm(seconds);
    pause();
    return 0;
} else {
    return alarm(0);
}

```
