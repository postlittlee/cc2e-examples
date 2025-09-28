```c
void openAndDo(char* fileName, void (*doTo)(FILE*)) {
  FILE* f = fopen(fileName, "r+");
  doTo(f);
  fclose(f);
}
```

```c
void show(FILE* f) {
  int c;
  while ((c=fgetc(f)) != EOF)
    putchar(c);
}

int main(int ac, char** av) {
  openAndDo("x.x", show);
}
```

```c
void appendLineEnd(FILE* f) {
  int c;
  int lastC;
  while ((c = fgetc(f)) != EOF) {
    lastC = c;
  }
  if (lastC != '\n')
    fputc('\n', f);
  }

int main(int ac, char** av) {
  openAndDo("x.x", appendLineEnd);
  openAndDo("x.x", show);
}
```
