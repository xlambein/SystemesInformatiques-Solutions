Basique

```c
int c_pid, status;

c_pid = fork();

if (c_pid == -1) {

    perror("fork");
    exit(EXIT_FAILURE);

}

if (c_pid == 0) {

    // Processus fils
    execv(path, argv);

} else {

    // Processus parent
    int p = waitpid(c_pid, &status, 0);

    if (p == -1) {

        perror("waitpid");
        exit(EXIT_FAILURE);

    }

    if (WIFEXITED(status)) {

        return WEXITSTATUS(status);

    }

    return -1;

}
```

Pipe

```c
int pipefd[2];
int status;
char buf;

if (pipe(pipefd) == -1) {

    perror("pipe");
    exit(EXIT_FAILURE);

}

int pid_a = fork();

if (pid_a == 0) {

    // Processus fils
    close(pipefd[0]);


    dup2(pipefd[1], STDOUT_FILENO);

    execv(path_a, argv_a);

}

int result = waitpid(pid_a, &status, 0);

if (result == -1) {

    perror("waitpid");
    exit(EXIT_FAILURE);

}

if (!WIFEXITED(status)) {

    exit(EXIT_FAILURE);
}

// Processus parent
close(pipefd[1]);

// On appelle le processus fils

int pid_b = fork();

if (pid_b == 0) {

    // Processus fils
    dup2(pipefd[0], STDIN_FILENO);

    execv(path_b, argv_b);

}

result = waitpid(pid_b, &status, 0);

if (WIFEXITED(status)) {

    return WEXITSTATUS(status);

} else {

    return -1;

}

```

ET Logique

```c
int result_a,
    result_b;

result_a = run_command(path_a, argv_a);

if (result_a != 0) {

    return result_a;

}

result_b = run_command(path_b, argv_b);

return result_b;
```

Reformater la sortie

```c
int status;

int c_pid = fork();

if (c_pid == -1) {

    perror("fork");
    exit(EXIT_FAILURE);

}

if (c_pid == 0) {

    // Processus fils
    dup2(open(output_path, "w"), STDOUT_FILENO);

    execv(path, argv);

}

// Processus parent
int result = waitpid(c_pid, &status, 0);

if (result == -1) {

    perror("waitpid");
    exit(EXIT_FAILURE);

}

if (WIFEXITED(status)) {

    return WEXITSTATUS(status);

} else {

    exit(EXIT_FAILURE);

}

```
