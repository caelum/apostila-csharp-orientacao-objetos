# Variáveis e tipos primitivos

Na maioria dos programas que escrevemos, não estamos interessados em apenas mostrar uma caixa de mensagens para o usuário. Queremos também armazenar e processar informações.

Em um sistema bancário, por exemplo, estaríamos interessados em armazenar o saldo de uma conta e o nome do correntista. Para armazenar esses dados, precisamos pedir para o C# reservar regiões de memória que serão utilizadas para armazenar informações. Essas regiões de memória são conhecidas como variáveis.

As variáveis guardam informações de um tipo específico. Podemos, por exemplo, guardar um número inteiro representando o número da conta, um texto para representar o nome do correntista ou um número real para representar o saldo atual da conta. Para utilizar uma variável, devemos primeiramente declará-la no texto do programa.

Na declaração de uma variável, devemos dizer seu tipo (inteiro, texto ou real, por exemplo) e, além disso, qual é o nome que usaremos para referenciá-la no texto do programa. Para declarar uma variável do tipo inteiro que representa o número de uma conta, utilizamos o seguinte código:

``` csharp
int numeroDaConta;
```

Repare no `;` no final da linha. Como a declaração de uma variável é um comando da linguagem C#, precisamos do `;` para terminá-lo.

Além do tipo `int` (para representar inteiros), temos também os tipos `double` e `float` (para números reais), `string` (para textos), entre outros.

Depois de declarada, uma variável pode ser utilizada para armazenar valores. Por exemplo, se estivéssemos interessados em guardar o valor `1` na variável `numeroDaConta` que declaramos anteriormente, utilizaríamos o seguinte código:

``` csharp
numeroDaConta = 1;
```

Lê-se "_numeroDaConta recebe 1_". Quando, no momento da declaração da variável, sabemos qual será seu valor, podemos utilizar a seguinte sintaxe para declarar e atribuir o valor para a variável.

``` csharp
double saldo = 100.0;
```

## Operações com variáveis

Agora que já sabemos como guardar informações no programa, estamos interessados em executar operações nesses valores. Pode ser interessante para um correntista saber qual será o saldo de sua conta após um saque de 10 reais. Para realizar essa operação, devemos subtrair 10 reais do saldo da conta:

``` csharp
double saldo = 100.0;
saldo = saldo - 10.0;
```

Nesse código, estamos guardando na variável saldo o valor da conta `100.0` (saldo antigo) menos `10.0` então seu valor final será de `90.0`. Da mesma forma que podemos subtrair valores, podemos também fazer somas (com o operador `+`), multiplicações (operador `*`) e divisões (operador `/`).

Podemos ainda guardar o valor do saque em uma variável:

``` csharp
double saldo = 100.0;
double valorDoSaque = 10.0;
saldo = saldo - valorDoSaque;
```

Depois de realizar o saque, queremos mostrar para o usuário qual é o saldo atual da conta. Para mostrarmos essa informação, utilizaremos novamente o `MessageBox.Show`:

``` csharp
MessageBox.Show("O saldo da conta após o saque é: " + saldo);
```

Veja que, no código do saque, estamos repetindo o nome da variável saldo dos dois lados da atribuição. Quando temos esse tipo de código, podemos utilizar uma abreviação disponibilizada pelo C#, o operador `-=`:

``` csharp
double saldo = 100.0;
double valorDoSaque = 10.0;
saldo -= valorDoSaque;
```

Quando o compilador do C# encontra o `saldo -= valorDoSaque`, essa linha é traduzida para a forma que vimos anteriormente: `saldo = saldo - valorDoSaque`. Além do `-=`, temos também os operadores `+=` (para somas), `*=` (para multiplicações) e `/=` (para divisões).

## Tipos Primitivos

Vimos que no C# toda variável possui um tipo, utilizamos o `int` quando queremos armazenar valores inteiros e `double` para números reais. Agora vamos descobrir quais são os outros tipos de variáveis do C#.

![ {w=85%}](assets/imagens/tipos-primitivos/tipos-primitivos-csharp.png)

Os tipos listados nessa tabela são conhecidos como **tipos primitivos** ou **value types** da linguagem C#. Toda vez que atribuímos um valor para uma variável de um tipo primitivo, o C# copia o valor atribuído para dentro da variável.

Agora que conhecemos os tipos primitivos da linguagem C#, vamos ver como é que eles interagem dentro de uma aplicação. Suponha que temos um código que declara uma variável do tipo inteiro e depois tenta copiar seu conteúdo para uma variável `long`:

``` csharp
int valor = 1;
long valorGrande = valor;
```

Nesse caso, como o tamanho de uma variável `long` é maior do que o de uma variável `int`, o C# sabe que podemos copiar o seu conteúdo sem perder informações e, por isso, esse é um código que compila sem nenhum erro. Agora vamos tentar copiar o `int` para uma variável do tipo `short`:

``` csharp
int valor = 1;
short valorPequeno = valor;
```

Nesse código, tentamos copiar o conteúdo de uma variável maior para dentro de uma de tamanho menor. Essa cópia pode ser perigosa pois o valor que está na variável do tipo `int` pode não caber na variável `short` e, por isso, o compilador do C# gera um erro de compilação quando tentamos fazer essa conversão.

Para forçarmos o compilador do C# a fazer uma conversão perigosa, precisamos utilizar uma operação do C# chamada **casting** falando para qual tipo queremos fazer a conversão.

``` csharp
int valor = 1;
short valorPequeno = (short) valor;
```

## Armazenando texto em variáveis

Além dos tipos primitivos, o C# também possui um tipo específico para armazenar textos. No tipo `string`, podemos guardar qualquer valor que seja delimitado por aspas duplas, por exemplo:

``` csharp
string mensagem = "Minha Mensagem";
MessageBox.Show(mensagem);
```

Podemos juntar o valor de duas variáveis do tipo string utilizando o operador `+` da linguagem. A soma de strings é uma operação conhecida como **concatenação**.

``` csharp
string mensagem = "Olá ";
string nome = "victor";

MessageBox.Show(mensagem + nome);
```

Esse código imprime o texto `Olá victor` em uma caixa de mensagens. Podemos utilizar a concatenação para adicionar o conteúdo de qualquer variável em uma `string`:

``` csharp
int idade = 25;
string mensagem = "sua idade é: " + idade;

MessageBox.Show(mensagem);
```

Esse segundo código imprime o texto `sua idade é: 25`.

## Documentando o código através de comentários

Quando queremos documentar o significado de algum código dentro de um programa C#, podemos utilizar comentários. Para fazermos um comentário de uma linha, utilizamos o `//`. Tudo que estiver depois do `//` é considerado comentário e, por isso, ignorado pelo compilador da linguagem.

``` csharp
double saldo = 100.0; // Isso é um comentário e será ignorado pelo compilador
```

Muitas vezes precisamos escrever diversas linhas de comentários para, por exemplo, documentar uma lógica complexa da aplicação. Nesses casos podemos utilizar o comentário de múltiplas linhas que é inicializado por um `/*` e terminado pelo `*/`. Tudo que estiver entre a abertura e o fechamento do comentário é ignorado pelo compilador da linguagem:

``` csharp
/*
 Isso é um comentário
 de múltiplas linhas
*/
```

## Exercícios

Faça o código dos exercícios do capítulo dentro de botões no formulário do projeto inicial, cada exercício deve ficar
na ação de um botão diferente.
1. Crie 3 variáveis com as idades dos seus melhores amigos e/ou familiares. Algo como:

	``` csharp
 int idadeJoao = 10;
 int idadeMaria = 25;
	```

	Em seguida, pegue essas 3 idades e calcule a média delas. Exiba o resultado em um MessageBox.

	
1. O que acontece com o código abaixo?

	``` csharp
 int pi = 3.14;
	```

	* O código compila, e "pi" guarda o número 3

	* O código compila, e "pi" guarda 3.14 (inteiros podem guardar casas decimais)

	* O código não compila, pois 3.14 não "cabe" dentro de um inteiro

	
1. Execute o trecho de código a seguir. O que acontece com ele?

	``` csharp
 double pi = 3.14;
 int piQuebrado = (int)pi;
 MessageBox.Show("piQuebrado = " + piQuebrado);
	```

	Repare o `(int)`. Estamos "forçando" a conversão do double para um inteiro.

	Qual o valor de `piQuebrado` nesse caso?

	* 3.14

	* 0

	* 3

	
1. (Opcional) No colegial, aprendemos a resolver equações de segundo grau usando a fórmula de Bhaskara. A fórmula é assim:

	```
 delta = b*b - 4*a*c;
 a1 = (-b + raiz(delta)) / (2 * a);
 a2 = (-b - raiz(delta)) / (2 * a);
	```

	Crie um programa com três variáveis inteiras, `a, b, c`, com quaisquer valores. Depois crie 3 variáveis double, `delta, a1, a2`, com a fórmula anterior.

	Imprima `a1` e `a2` em um MessageBox.

	Dica: Para calcular raiz quadrada, use `Math.Sqrt(variavel)`. Não se esqueça que não podemos calcular a raiz quadrada de números negativos.

	
