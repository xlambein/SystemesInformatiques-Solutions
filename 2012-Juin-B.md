# Juin 2012 B
## Theorie

### Question 1: Reader-Writer

Le problème des Reader-Writer consiste à partager une ressource en mémoire entre plusieurs lecteurs (qui ne modifieront pas la ressources) et des writer (qui veulent modifier la ressource).  
Plusieurs readers peuvent lire la ressource simultanément, mais un writer **doit** être le seul à avoir accès à la ressource. De plus, quand un writer attend que les readers finissent de lire la ressource, aucun nouveau reader ne peut commencer à la lire (le writer est prioritaire), pour éviter que les readers ne prennent possession de la ressource indéfiniement.

Implémentation:
```c
int read_count = 0;
int write_count = 0;
pthread_mutex_t read_count_mutex;
pthread_mutex_t write_count_mutex;
pthread_mutex_t z;
sem_t wsem;
sem_t rsem;


// Reader
pthread_mutex_lock(&z);
sem_wait(&rsem);

pthread_mutex_lock(&read_count_mutex);

readcount++;
if (readcount == 1) {
	sem_wait(&wsem);
}

pthread_mutex_unlock(&read_count_mutex);

sem_post(&rsem);
pthread_mutex_unlock(&z);

section_critique_read();

pthread_mutex_lock(read_count_mutex);

readcount--;
if (readcount == 0) {
	sem_post(&wsem);
}

pthread_mutex_unlock(read_count_mutex);




// Writer
pthread_mutex_lock(&write_count_mutex);

write_count++;
if (write_count == 1) {
	sem_wait(&rsem);
}

pthread_mutex_unlock(&write_count_mutex);

sem_wait(&wsem);

section_critique_write();

sem_post(&wsem);

pthread_mutex_lock(&write_count_mutex);

write_count--;
if (write_count == 0) {
	sem_post(&rsem);
}

pthread_mutex_unlock(&write_count_mutex);

```

### Question 2: Threads vs Processus

Un thread est crée via la fonction `pthread_create`. Un processus est crée via la fonction `fork`

On peut terminer l'execution d'un thread ou d'un processus en retournant une valeur dans la fonction `main`.  
Dans le cas d'un thread, on peut aussi le terminer en utilisant `pthread_exit`.  
Dans le cas d'un processus, on peut aussi le terminer via `exit`.

Pour attendre et récuperer la valeur de retour d'un thread, on peut utiliser `pthread_join`.  
Dans le cas d'un processus, on peut utiliser `waitpid`.

Deux threads du même processus peuvent échanger de l'information via les variables globales ainsi que les variables allouées sur le heap.  
Deux processus peuvent échanger de la mémoire via des pages partagées en utilisant le mécanisme de mémoire virtuelle.

Pour le programme A, la partie code, données non-initialisés/initialisés et le heap sont commune, le stack est unique aux threads.
Pour le programme B, la partie code est commune, tout le reste est unique aux processus.  

## Exercices

### Question 1: Corriger le code

### Question 2: 

### Question 3: 

