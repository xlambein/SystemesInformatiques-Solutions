```c
// Recupere la permission du fichier d'origine
struct stat chmod_from;
stat(fname_from, &chmod_from);

// Ouvre les 2 fichiers (applique aussi les permissions du fichier d'arrivee si celui-ci
// est cree)
int fdesc_from = open(fname_from, O_RDONLY);
int fdesc_to   = open(fname_to, O_WRONLY|O_CREAT, chmod_from.st_mode);

if (fdesc_from == -1 || fdesc_to == -1) {
    perror("open");
    return -1;
}

// On copie le contenu du fichier
char *buf = "po";

while (read(fdesc_from, (void *) &buf, sizeof(char)) > 0) {

    write(fdesc_to, (void *) &buf, sizeof(char));

}

close(fdesc_from);
close(fdesc_to);

return 0;
```

On est obligé de mettre une réponse

```c
void placeholder() {

}
```
