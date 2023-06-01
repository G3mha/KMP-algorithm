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

??? Exercício 0

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


Agora vamos tentar encontrar uma maneira de reduzir o número de comparações redundantes realizadas no algoritmo, para isso vamos identificar quais comparações são realmente necessárias e quais podemos evitar.

??? Exercício 1
:EX1

Vamos supor que estamos buscando a string ABCD no texto e acabamos de realizar a comparação sinalizada na imagem acima. É necessário continuar a busca a partir de qual elemento do texto?

::: Gabarito

Podemos ver imediatamente que a string buscada não se repete em nenhum momento, se sabemos disso assim como o fato que tivemos comparações de sucesso dos elementos B e C, podemos deduzir com certeza que nenhum dos valores que foram comparados a eles é igual ao primeiro elemento. Assim podemos fazer um salto na comparação e retomá-la da seguinte forma:

:GAB1

:::
???

??? Exercício 2
:EX2

E neste caso, qual deve ser a próxima comparação necessária a se fazer?

::: Gabarito

Neste caso temos um raciocínio similar ao anterior, mas com condições opostas. Se sabemos que nossa string buscada se repete no segundo elemento e encontramos uma diferença ao compará-lo, sabemos que esta posição no texto não pode ser igual ao primeiro elemento da string buscada. Assim, podemos fazer um salto na comparação e retomá-la da seguinte forma:

:GAB2
:::
???

??? Exercício 3
:EX3

Vamos analisar um último caso para garantir que este raciocínio realmente funciona. Qual deve ser a próxima comparação necessária a se fazer?

::: Gabarito

Se você estava atento deve ter reparado que os elementos AB aparecem duas vezes juntos então, apesar da comparação ter falhado neste intervalo por causa do último termo, essa segunda aparição de AB pode ser ainda um outro começo para a string buscada. Assim o salto deve ser feito considerando essa repetição, da seguinte forma: 

:GAB3

:::
???

Certo, agora que temos uma ideia de como podemos reduzir o número de comparações, vamos tentar generalizar este raciocínio para criar um algoritmo que faça isso automaticamente.

Para isso vamos precisar entender os conceitos de prefixo e sufixo.

Prefixos e sufixos
---------
Utilizando o padrão buscado podemos construir vários prefixos, isto é, qualquer sequência de elementos encontrada no início da string buscada. Por exemplo, para a string ABCD temos os seguintes prefixos:

:Prefixos1

Da mesma forma podemos pegar cada um desses prefixos e construir seus sufixos, isto é, qualquer sequência de elementos encontrada no final de cada um dos prefixos. Para o caso anterior temos os seguintes sufixos:

:Prefixos2


??? Exercício 4

:EX4

Antes de prosseguirmos com o uso destes conceitos, vamos praticar um pouco. Quais são os prefixos e sufixos da string acima?

::: Gabarito
:GAB4

:::
???

Agora que sabemos criar os prefixos e sufixos precisamos descobrir como utilizá-los para reduzir o número de comparações.


Vamos voltar ao caso anterior:

:EX3

??? Exercício 5
Neste caso a última comparação foi feita com o prefixo ABCAB, qual o sufixo deste prefixo que também é um prefixo da string buscada?

::: Gabarito
Consultando a tabela de prefixos e sufixos podemos ver que o sufixo que também é um prefixo da string buscada é AB.

:GAB5

:::
???

??? Exercício 6

Que tipo de operação podemos fazer com este prefixo e sufixo para determinar o tamanho salto que deve ser feito na comparação?

::: Gabarito
A última comparação realizada, foi com um prefixo de tamanho 5. O sufixo encontrado que também é um prefixo da string buscada tem tamanho 2. Assim, podemos fazer um salto de 5 - 2 = 3 posições na comparação. Resultando na mesma comparação exibida anteriormente:

:GAB3

:::
???

Se você entendeu tudo isso **PARABÉNS!** você aprendeu a lógica do algorítmo de Knuth-Morris-Pratt. A implementação é um pouco mais difícil e utiliza um vetor de repetições (também chamado de tabela de falhas), mas não se preocupe, vamos ver como ele funciona e como implementá-lo.


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
