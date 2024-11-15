# 2296

#include<stdio.h>
#include<string.h>

struct stack {
    char s[20];
    int top;
};

struct stack st;

int isempty() {
    return (st.top == 1); // Corrected from 1 to 0
}

void push(char p) {
    st.s[st.top++] = p;
}

char pop() {
    if (isempty()) {
        printf("Stack empty\n");
        return '\0';
    } else {
        return st.s[--st.top]; // Corrected decrement position
    }
}

void disp() {
    int i;
    for (i = 0; i < st.top; i++) {
        printf("%c", st.s[i]);
    }
}

int reduce(char rp[10][10], int n) {
    int i, t, k;
    char u[10];
    t = st.top - 1;

    for (i = 0; i <= t; i++) { // Corrected loop condition to t instead of st.top
        u[i] = st.s[t - i]; // Added index fix to access from top
        u[i + 1] = '\0';

        for (k = 0; k < n; k++) {
            if (strcmp(rp[k], u) == 0) {
                st.top -= i + 1;
                return k;
            }
        }
    }

    return 99;
}

int shift(char ip[], int *j) {
    push(ip[*j]);
    (*j)++;
    disp();
    return 1;
}

int main() {
    int n, i, j = 0, k, h;
    char lp[10];
    char ip[10];
    char rp[10][10];
    st.top = 0;

    printf("\nEnter the number of productions: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++) {
        printf("\nEnter the left side of the production %d: ", i + 1);
        scanf(" %c", &lp[i]);
        printf("\nEnter the right side of the production %d: ", i + 1);
        scanf("%s", rp[i]);
    }

    printf("\nEnter the input: ");
    scanf("%s", ip);
    printf("==================================================================\n");
    printf("\nSTACK\t\t\tINPUT\t\t\tOUTPUT\n");
    printf("==================================================================\n");

    strcat(ip, "$");
    push('$');
    printf("$\t\t\t%s\n", ip);

    while (!(st.s[st.top - 1] == lp[0] && st.s[st.top - 2] == '$' && (j == (strlen(ip) - 1)) && st.top == 2)) {
        if ((h = reduce(rp, n)) != 99) {
            push(lp[h]);
            disp();
            printf("\t\t\t");
            for (k = j; k < strlen(ip); k++)
                printf("%c", ip[k]);
            printf("\t\t\tReduce %c -> %s\n", lp[h], rp[h]);
        } else if (shift(ip, &j)) {
            printf("\t\t\t");
            for (k = j; k < strlen(ip); k++)
                printf("%c", ip[k]);
            printf("\t\t\tShift %c\n", ip[j - 1]);
        }
    }

    disp();
    printf("\t\t\t");
    for (k = j; k < strlen(ip); k++)
        printf("%c", ip[k]);
    printf("\t\t\tAccept\n");

    return 0;
}

-----------------------


#include <stdio.h>
#include <ctype.h>
#include <string.h>

struct symtab {
    int lineno;
    char var[25];
    char dt[25];
    char val[10];
} sa[20];

int main() {
    int i = 0, max = 0, line = 0;
    char s[25], typ[25], gar[] = "garbage";
    FILE *fn;

    fn = fopen("input.txt", "r");
    if (fn == NULL) {
        printf("Error: Could not open file.\n");
        return 1;
    }

    printf("\n\nSYMBOL TABLE MANAGEMENT\n\n");
    printf("Variable\tDatatype\tLine.no.\tValue\n");

    while (!feof(fn)) {
        fscanf(fn, "%s", s);

        // Check for data type declarations
        if ((strcmp(s, "int") == 0) || (strcmp(s, "float") == 0) || (strcmp(s, "char") == 0)) {
            strcpy(typ, s);
            line++;

            while (1) {
                i++;
                max = i;
                sa[i].lineno = line;
                
                // Read variable name
                fscanf(fn, "%s", s);
                strcpy(sa[i].var, s);
                strcpy(sa[i].dt, typ);

                // Check if there's an assignment
                fscanf(fn, "%s", s);
                if (strcmp(s, "=") == 0) {
                    fscanf(fn, "%s", s);
                    strcpy(sa[i].val, s);  // Assign value to the variable
                    fscanf(fn, "%s", s);   // Read the next character (either ',' or ';')
                } else {
                    strcpy(sa[i].val, gar);  // Assign "garbage" if no value is assigned
                }

                // If ',' continue to next variable, if ';' end declaration
                if (strcmp(s, ",") == 0) {
                    continue;
                } else if (strcmp(s, ";") == 0) {
                    break;
                }
            }
        }
    }

    // Print the symbol table
    for (i = 1; i <= max; i++) {
        printf("\n%s\t\t%s\t\t%d\t\t%s\n", sa[i].var, sa[i].dt, sa[i].lineno, sa[i].val);
    }

    fclose(fn);
    return 0;
}
