# Juin 2012 B
## Theorie

### Question 1: Reader-Writer

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

