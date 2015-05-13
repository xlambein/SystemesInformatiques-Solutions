```c
if (stringp == NULL || *stringp == NULL)
    return NULL;

char *cur = *stringp;
char *beginning = *stringp;

while (*cur != '\0') {

    if (delim != NULL && *cur == *delim) {
       *stringp = cur + 1;
       *cur = '\0';

        return beginning;

    }

    cur = cur + 1;
}

// On est arrivé au bout sans trouver le délimiteur
*stringp = NULL;
return beginning;
```
