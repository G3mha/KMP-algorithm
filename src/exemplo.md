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

int busca_ingenua(char* texto, char* string) {
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

Criando o vetor de repetições
---------

Para começar a implementar o KMP, devemos criar um vetor de repetições, que consiste em um vetor de inteiros que armazena o tamanho do maior prefixo que também é sufixo para cada posição da string buscada.

Esta é a construção visual do vetor de repetições:

:Vetor

Abaixo, temos a implementação em C de uma funcão que cria o vetor de repetições:

``` C
#include <stdio.h>
#include <string.h>

void cria_vetor_repeticoes(char* pattern, int M, int* pps) {
   // pattern é a string buscada
   // M é a quantidade de caracteres da string buscada
   // pps é o vetor de repetições
   int length = 0;
   pps[0] = 0;
   int i = 1;
   while (i < M) {
      if (pattern[i] == pattern[length]) {
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

Implementando o KMP
---------

Com o vetor de repetições criado, estamos livres para implementar o algoritmo KMP. Para isso, basta percorrer o texto e a string buscada simultaneamente, comparando os caracteres. Caso os caracteres sejam iguais, incrementamos o índice do texto e o índice da string buscada. Caso contrário, incrementamos apenas o índice do texto.

Essa explicação pode ser confusa à primeira vista, por isso preste atenção na simulação visual algoritmo.

!!! Aviso
Vale-se notar que cada imagem não se trata de uma iteração, visto que as comparações e incremento de i e j acontecem dentro de uma única iteração.
!!!

:KMP

Complementando a explicaçao visual, temos a implementação em C do algoritmo KMP:

``` C
#include <stdio.h>
#include <string.h>

void busca_KMP(char* texto, char* string) {
   int M = strlen(string);
   int N = strlen(texto);
   int pps[M];
   cria_vetor_repeticoes(string, M, pps);
   int i = 0;
   int j = 0;
   while (i < N) {
      if (string[j] == texto[i]) {
         j++;
         i++;
      }
      if (j == M) {
         printf("Found string at index %d\n", i - j);
         j = pps[j - 1];
      }
      else if (i < N && string[j] != texto[i]) {
         if (j != 0)
         j = pps[j - 1];
         else
         i = i + 1;
      }
   
   }
}

int main() {
   char texto[] = "AACTGGACGACACTAA";
   char string[] = "ACAC";
   busca_KMP(texto, string);
   return 0;
}
```

Comparando ao algoritmo ingênuo
---------

TODO

Implementação matemática
---------

TODO

Aplicações reais
---------

O algoritmo KMP é utilizado em diversas aplicações, como:

- Busca de padrões em textos
- Busca de padrões em imagens
- Busca de padrões em vídeos
- Busca de padrões em áudios
- Busca de padrões em DNA
- Busca de padrões em proteínas
- Busca de padrões em redes de computadores
- Busca de padrões em sistemas de arquivos
- Busca de padrões em sistemas de banco de dados
- Busca de padrões em sistemas de compressão de dados
- Busca de padrões em sistemas de criptografia
- Busca de padrões em sistemas de processamento de sinais
- Busca de padrões em sistemas de reconhecimento de voz
- Busca de padrões em sistemas de reconhecimento de escrita
- Busca de padrões em sistemas de reconhecimento de fala

Ou seja, basicamente tudo que involve busca de padrões, em que este padrão seja representado para uma sequência de caracteres.
