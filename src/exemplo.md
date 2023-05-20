[Desafios de Programação](https://ensino.hashi.pro.br/desprog/)

Algoritmo Knuth-Morris-Pratt (KMP)
======

História
---------

O algoritmo KMP foi primeiramente concebido por James H. Morris e, pouco tempo depois, descoberto independentemente por Donald Knuth (autor de _The Art of Computer Programming_), a partir da teoria dos autômatos (que consiste em desenvolver algoritmos para buscar palavras em texto). James Morris e Vaughan Pratt publicaram um relatório técnico sobre em 1970. O trio também publicou o algoritmo conjuntamente em 1977.

Contexto
---------

Este handout apresenta o funcionamento do algoritmo KMP e sua comparação ao algoritmo ingênuo de busca. Assim, destrincharemos sua implementação em C.

O KMP consiste em um algoritmo de busca de strings que tem como pior caso a complexidade de tempo linear O(n).

A aplicação deste algoritmo tem duas etapas:

1. Criação do vetor de repetições
2. Busca no texto

Algoritmo ingênuo
---------

Antes de demonstrar o funcionamento do algoritmo KMP é importante apresentar primeiro um algoritmo ingenuo que consiste em dois loops: um externo para percorrer o texto e um interno para percorrer a palavra buscada.

A complexidade deste algoritmo é O(nm), onde n é o tamanho do texto e m o tamanho da palavra buscada.

!!! Aviso
Vale ressaltar que quanto maior o número de elementos do vetor, maior é a sua ineficiência.
!!!

Esta é uma simulação do código ingênuo:

:Naive

??? Exercício 1

Qual a implementação, em pseudo-código, para o algoritmo de busca ingênuo?

::: Gabarito

``` pseudocode
começo algoritmo
   para cada caractere do texto
      se o caractere atual for igual ao primeiro caractere da string
         para cada caractere da string
            se o caractere atual do texto não for igual ao caractere atual da string
               sai do loop interno
            fim se
            se todos os caracteres da string foram encontrados no texto
               retorna a posição do primeiro caractere da string no texto
            fim se
         fim para
      fim se
   fim para
   retorna -1
fim algoritmo
```

:::

???

??? Exercício 2

Agora, dê a implementação, em C, para o algoritmo de busca ingênuo.

::: Gabarito

``` C
#include <stdio.h>
#include <string.h>

int buscaIngenua(char* texto, char* string) {
   int i, j;
   int M = strlen(string);
   int N = strlen(texto);
   for (i = 0; i <= N - M; i++) {
      for (j = 0; j < M; j++)
         if (texto[i + j] != string[j])
            break;
      if (j == M)
         return i;
   }
   return -1;
}
```

:::

???

Funcionamento
---------

TODO

Implementação matemática
---------

TODO

Implementação em C
---------

TODO

Comparando ao algoritmo ingênuo
---------

TODO

Aplicações reais
---------

TODO


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