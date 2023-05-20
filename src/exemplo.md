[Desafios de Programação](https://ensino.hashi.pro.br/desprog/)

Algoritmo Knuth-Morris-Pratt (KMP)
======

Contexto
---------

Este handout apresenta o funcionamento do algoritmo KMP e sua comparação ao algoritmo ingênuo de busca. Assim, destrincharemos sua implementação em C.

História
---------

O algoritmo KMP foi primeiramente concebido por James H. Morris e, pouco tempo depois, descoberto independentemente por Donald Knuth (autor de _The Art of Computer Programming_), a partir da teoria dos autômatos (que consiste em desenvolver algoritmos para buscar palavras em texto). James Morris e Vaughan Pratt publicaram um relatório técnico sobre em 1970. O trio também publicou o algoritmo conjuntamente em 1977.

Implementação matemática
---------

Ele consiste em um algoritmo de busca de strings que tem como pior caso a complexidade de tempo linear O(n).

A aplicação deste algoritmo tem duas etapas:

1. Criação do vetor de repetições
2. Busca no texto

Para tabelas, usa-se a [notação do
MultiMarkdown](https://fletcher.github.io/MultiMarkdown-6/syntax/tables.html),
que é muito flexível. Vale a pena abrir esse link para saber todas as
possibilidades.

| coluna a | coluna b |
|----------|----------|
| 1        | 2        |


Antes de demonstrar o funcionamento do algoritmo KMP é importante apresentar primeiro um algoritmo ingenuo que consiste em dois loops: um externo para percorrer o texto e um interno para percorrer a palavra buscada.

Esta é uma simulação do código ingênuo:

:Naive


Esta é a construção do vetor de repetições:

:Vetor

E então uma simulação da aplicação do algoritmo KMP (vale-se notar que cada imagem não se trata de uma iteração, visto que as comparações e incremento de i e j acontecem dentro de uma única iteração)

:KMP

Código de aplicação do algorítmo em C

``` c
#include<stdio.h>
#include<string.h>
void KMPAlgorithm(char* text, char* pattern) {
   int M = strlen(pattern);
   int N = strlen(text);
   int pps[M];
   prefixSuffixArray(pattern, M, pps);
   int i = 0;
   int j = 0;
   while (i < N) {
      if (pattern[j] == text[i]) {
         j++;
         i++;
      }
      if (j == M) {
         printf("Found pattern at index %d\n", i - j);
         j = pps[j - 1];
      }
      else if (i < N && pattern[j] != text[i]) {
         if (j != 0)
         j = pps[j - 1];
         else
         i = i + 1;
      }
   
   }
}
int main() {
   char text[] = "AACTGGACGACACTAA";
   char pattern[] = "ACAC";
   KMPAlgorithm(text, pattern);
   return 0;
}
```

Código de criação do vetor de repetições em C

``` c
#include<stdio.h>
#include<string.h>

void prefixSuffixArray(char* pat, int M, int* pps) {
   int length = 0;
   pps[0] = 0;
   int i = 1;
   while (i < M) {
      if (pat[i] == pat[length]) {
         length++;
         pps[i] = length;
         i++;
      } else {
         if (length != 0)
         length = pps[length - 1];
         else {
            pps[i] = 0;
            i++;
         }
      }
   
    
   }
}
```


!!! Aviso
Este é um exemplo de aviso, entre `md !!!`.
!!!


??? Exercício

Este é um exemplo de exercício, entre `md ???`.

::: Gabarito
Este é um exemplo de gabarito, entre `md :::`.
:::

???


**Lembrar de remover estruturas de exemplos**