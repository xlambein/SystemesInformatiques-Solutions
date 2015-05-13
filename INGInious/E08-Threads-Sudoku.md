Je sais pas si ma solution est exactement ce qui est attendu pour l'exerice:
Il y a tout simplement 3 fonctions (solve_block, solve_row, solve_column) qui vérifie
pour un bloc, une rangée ou une colonne si il est possible de remplir un nombre
de manière sûre (C'est à dire si il n'y a qu'une seule solution possible).

Un thread va tester les 9 blocs, 9 rangées et 9 colonnes continuellement en essayant
de remplir ce qu'il peut (ceci peut mener à des livelock si jamais le sudoku est
trop complexe).



```c
pthread_t threads[nthread];

int i;
for (i = 0;i < nthread;i++) {

    pthread_create(&threads[i], NULL, solver, s);

}

// On attend que le sudoku soit resolue
for (i = 0;i < nthread;i++) {

    pthread_join(threads[i], NULL);

}

return;
```

```c
typedef struct {
    int x, y;
    int value;
} sudoku_value_t;

// Verifie si un groupe de nombre (bloc, ligne ou colonne) contient un seul
// carre "blanc" (represente par un 0)
int get_lone_value(int values[]) {

    int lone_value = 0;
    int i;

    for (i = 1;i <= SUDOKU_SIZE;i++) {

        if (!values[i-1]) {

            if (lone_value == 0) {

                // Premier carre blanc qu'on trouve
                lone_value = i;

            } else {

                // On avait deja trouve un carre blanc, il y a donc 2 carre
                // blancs
                return 0;

            }

        }

    }

    return lone_value;

}

void set_value(sudoku_t *s, sudoku_value_t value) {

    s->grid[value.y][value.x] = value.value;

}

sudoku_value_t check_block(sudoku_t *s, int x_3, int y_3) {

    int i, j;
    // Tableau contenant un int-boolean pour chaque nombre
    // Si values[4] == 1, alors 4 est connu dans ce block
    int values[SUDOKU_SIZE];

    for (i = 0;i < SUDOKU_SIZE;i++)
        values[i] = 0;

    int blank_value_x = 0,
        blank_value_y = 0;

    for (j = y_3 * 3;j < y_3 * 3 + 3;j++) {

        for (i = x_3 * 3;i < x_3 * 3 + 3;i++) {

            int value_at_coord = s->grid[j][i];

            if (value_at_coord != 0) {

                // Ce nombre est deja connue
                values[value_at_coord - 1] = 1;

            } else {

                // Ce nombre n'est pas connue
                blank_value_x = i;
                blank_value_y = j;

            }

        }

    }

    int lone_value = get_lone_value(values);

    sudoku_value_t value = {
        .x = blank_value_x,
        .y = blank_value_y,

        .value = lone_value
    };

    return value;
}

sudoku_value_t check_column(sudoku_t *s, int x) {

    int j, i;
    // Tableau contenant un int-boolean pour chaque nombre
    // Si values[4] == 1, alors 4 est connu dans ce block
    int values[SUDOKU_SIZE];

    for (i = 0;i < SUDOKU_SIZE;i++)
        values[i] = 0;

    int blank_value_x = 0,
        blank_value_y = 0;

    for (j = 0;j < SUDOKU_SIZE;j++) {

        int value_at_coord = s->grid[j][x];

        if (value_at_coord != 0) {

            // Ce nombre est deja connue
            values[value_at_coord - 1] = 1;

        } else {

            // Ce nombre n'est pas connue
            blank_value_x = x;
            blank_value_y = j;

        }

    }

    int lone_value = get_lone_value(values);

    sudoku_value_t value = {
        .x = blank_value_x,
        .y = blank_value_y,
        .value = lone_value
    };

    return value;
}

sudoku_value_t check_row(sudoku_t *s, int y) {

    int i;
    // Tableau contenant un int-boolean pour chaque nombre
    // Si values[4] == 1, alors 4 est connu dans ce block
    int values[SUDOKU_SIZE];

    for (i = 0;i < SUDOKU_SIZE;i++)
        values[i] = 0;

    int blank_value_x = 0,
        blank_value_y = 0;

    for (i = 0;i < SUDOKU_SIZE;i++) {

        int value_at_coord = s->grid[y][i];

        if (value_at_coord != 0) {

            // Ce nombre est deja connue
            values[value_at_coord - 1] = 1;

        } else {

            // Ce nombre n'est pas connue
            blank_value_x = i;
            blank_value_y = y;

        }

    }

    int lone_value = get_lone_value(values);

    sudoku_value_t value = {
        .x = blank_value_x,
        .y = blank_value_y,
        .value = lone_value
    };

    return value;
}

int is_finished(sudoku_t *s) {

    int i, j;
    for (j = 0;j < SUDOKU_SIZE;j++) {

        for (i = 0;i < SUDOKU_SIZE;i++) {

            if (s->grid[j][i] == 0)
                return 0;

        }

    }

    return 1;

}

void solve_row(sudoku_t *s) {

    int y;
    sudoku_value_t value;

    for (y = 0;y < SUDOKU_SIZE;y++) {

        value = check_row(s, y);

        if (value.value != 0) {

            // On a trouve une valeur
            set_value(s, value);

        }

    }
}

void solve_block(sudoku_t *s) {

    int x;
    sudoku_value_t value;

    for (x = 0;x < SUDOKU_SIZE;x++) {

        value = check_column(s, x);

        if (value.value != 0) {

            // On a trouve une valeur
            set_value(s, value);

        }

    }
}

void solve_column(sudoku_t *s) {

    int x, y;
    sudoku_value_t value;

    for (x = 0;x < 3;x++) {

        for (y = 0;y < 3;y++) {

            value = check_block(s, x, y);

            if (value.value != 0) {

                // On a trouve une valeur
                set_value(s, value);

            }

        }

    }
}

void *solver(void * sudoku_raw) {

    sudoku_t *sudoku = (sudoku_t *) sudoku_raw;

    while (!is_finished(sudoku)) {

        solve_row(sudoku);

        solve_column(sudoku);

        solve_block(sudoku);

    }

    return NULL;

}
```
