```c
pthread_mutex_init(&mutex, NULL);
sem_init(&empty, 0, N);
sem_init(&full, 0, 0);

```

```c
int i,
    item;

for (i = 0;i < max;i++) {

    item = produce();

    sem_wait(&empty);
    pthread_mutex_lock(&mutex);

    insert(item);

    pthread_mutex_unlock(&mutex);
    sem_post(&full);

}
```
