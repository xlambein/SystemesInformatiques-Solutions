# Juin 2012 A
## Theorie

### Question 1: Peterson
L'implémentation:
```c
int last = 0;
int in1 = false;
int in2 = false;


// Thread 1
in1 = true;
last = 1;
while (in2 && last == 1) { }
section_critique();
in1 = false;

// Thread 2
in2 = true;
last = 2;
while (in1 && last == 2) { }
section_critique();
in2 = false;
```
On peut facilement synchroniser 2 threads distincts, il suffit de rendre les variables `last`, `in1` et `in2` globales.
Pour synchronisez 2 processus différents, il faudra trouver un moyen de partager ces variables, via une zone de mémoire (page) partagée par exemple.

### Question 2: Mémoire
Suffit de voir le cours pour le schéma:

* Code: Contient les instructions qui seront exécuter par le processeur.
* Données initialisées: Contient les constantes, les strings ainsi que les variables globales initialisées avec une valeur par défaut utilisés dans le programme
* Données non-initialisées: Contient les variables globales qui ne sont pas initialisées.
* Tas (Heap): Contient la mémoire allouée via les `malloc`
* [Espace vide]
* Pile (Stack): Contient les variables locales utilisées par les fonctions. A chaque appel de fonction, une zone de mémoire est reservée au dessus du stack pour contenir les variables locales utilisées durant l'execution de la fonction.

## Exercices

### Question 1: Corriger le code

* L'argument 2 et 3 de `strtol` doivent être inversés.
* Dans l'appel de `sortme`, il ne faut pas utiliser le `&` puisque `orig` et `sorted` sont déjà des `int *`

### Question 2: fonction copy
Réponse d'Alexis Clarembeau
```c
int copy(const char *from, const char *to){
        int source = open(from, O_RDONLY);
        if(source <= 0){
                perror("Open error: ");
                return 0;
        }
       
        int dest = open(to, O_WONLY | O_CREAT);
        if(dest <= 0){
                perror("Open error: ");
                return 0;
        }
       
        char c;
        while(read(source, &c, sizeof(char)) == 1){
                if(write(source, &c, sizeof(char) == 1)){
               
                }
                else{
                        perror("Write error: ");
                        close(source);
                        close(dest);
                        return 0;
                }
        }
        close(source);
        close(dest);
        return 1;
}
 
void *fun(void *argvoidptr){
        copy_arg_t *arg = (copy_arg_t *) argvoidptr;
        char *destdir = arg->dest;
       
        int i;
       
        for(i = 0 ; i < arg->n; i++){
                char *srcfile = arg->files[i];
                char dest_file[MAX_PATH_LEN];
               
                snprintf(dest_file, MAX_PATH_LEN, "%s/%s", destdir, extract_filename(srcfile));
               
                copy(srcfile, dest_file);
        }
       
       
 
        free(arg->files);
        free(arg);
        return NULL;
}

```

### Question 3: fonction main

```c

int main(int argc, char **argv){
        // ................................
        // ........ code précédent ........
        // ................................
       
        int i = 0;
        int j = 0;
       
        pthread_t *threads = malloc(sizeof(pthread_t) * n_threads);
       
        // on lit les arguments passés en commande
                // le nom de dossier est le premier argument
                char *dirname = argv[optind];
       
                int n_files = argc-(optind+1);
       
                int k = 0;
               
                // on extrait les noms de chaque fichiers
                char **filespath = malloc(sizeof(char*) * n_files);
                for(i = optind +1 ; i < argc ; i++){
                        filespath[k] = argv[i];
                        k++;
                }
       
        int filecursor = 0;
       
        // on lance les threads
        for(i = 0 ; i < n_threads ; i++){
                // on passe les arguments
                        copy_arg_t *arg = malloc(sizeof(copy_arg_t));
                        arg->id = i+1;
                        arg->dest = dirname;
               
                // on attribue les fichiers à chaque thread
                        int files_per_thread = n_files / n_threads; // !! division entière
                        // combien de fichiers
                        arg->n = files_per_thread;
                        if(i == n_threads -1){
                                arg->n += n_files - (files_per_thread * n_threads);
                        }
                        // quels fichiers?
                        arg->files = malloc(sizeof(char*) * arg->n);
                        for(j = 0 ; j < arg->n ; j++){
                                arg->files[j] = filespath[filecursor];
                                filecursor++;
                        }
               
                // on peut alors lancer le thread
                        pthread_create(&threads[i], NULL, &fun, arg);
        }
       
        // on attend le résultat
        for(i = 0 ; i < n_threads ; i++){
                pthread_join(threads[i], NULL);
        }
       
        // NB: on ne doit pas free les noms de fichiers car ils appartiennent à argv
                // memo: on free tout ce qui est malloc ;)
        free(threads);
        free(filespath);
}
```
