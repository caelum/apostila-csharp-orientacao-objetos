# Estruturas de repetição

## Repetindo um bloco de código

De volta ao exemplo da aula anterior, suponha agora que o cliente desse mesmo banco queira saber quanto ele ganhará, ao final de 1 ano, caso ele invista um valor. O investimento paga 1% do valor investido ao mês.

Por exemplo, se o cliente investir R$ 1000,00, ao final de 12 meses, terá por volta de R$ 1126,82: no primeiro mês, R$ 1000,00 + R$1000,00 * 1% = R$ 1010,00; no segundo mês, R$ 1010,00 + R$1010,00 * 1% = R$ 1020,10; e assim por diante. Ou seja, para calcular o quanto ele terá ao final de um ano, podemos multiplicar o valor investido 12 vezes por 1%.

Para resolvermos esse problema, precisamos fazer uso de uma estrutura de controle que repete um determinado bloco de código até que uma condição seja satisfeita. Essa estrutura recebe o nome de loop.

Para fazer um loop no C#, utilizaremos, inicialmente, a instrução `for`. O for é uma instrução que possui três partes:


* A primeira parte é a inicialização, na qual podemos declarar e inicializar uma variável que será utilizada no `for`;
* A segunda parte é a condição do loop. Enquanto a condição do loop for verdadeira, o loop continuará executando;
* A terceira parte é a atualização, na qual podemos atualizar as variáveis que são utilizadas pelo for.


Cada uma das partes do `for` é separada por um `;`.

``` csharp
for (inicialização; condição; atualização)
{
    // Esse código será executado enquanto a condição for verdadeira
}
```

Veja o código a seguir, por exemplo, em que usamos um `for` que repetirá o cálculo 12 vezes:

``` csharp
double valorInvestido = 1000.0;
for (int i = 1; i <= 12; i += 1)
{
    valorInvestido = valorInvestido * 1.01;
}
MessageBox.Show("Valor investido agora é " + valorInvestido);
```

Veja que nosso `for` começa inicializando a variável `i` com 1 e repete o código de dentro dele enquanto o valor de `i` for menor ou igual a 12, ou seja, ele só para no momento em que `i` for maior do que 12. E veja que, a cada iteração desse loop, o valor de `i` cresce (`i += 1`). No fim, o código de dentro do `for` será repetido 12 vezes, como precisávamos.

O mesmo programa poderia ser escrito utilizando-se um `while`, em vez de um `for`:

``` csharp
double valorInvestido = 1000.0;
int i = 1;
while (i <= 12)
{
    valorInvestido = valorInvestido * 1.01;
    i += 1;
}
MessageBox.Show("Valor investido agora é " + valorInvestido);
```

## Para saber mais do while

No C# quando utilizamos o `while`, a condição do loop é checada antes de todas as voltas (iterações) do laço, mas e se quiséssemos garantir que o corpo do laço seja executado pelo menos uma vez? Nesse caso, podemos utilizar um outro tipo de laço do C# que é o `do while`:

``` csharp
do
{
    // corpo do loop
}
while(condição);
```

Com o `do while` a condição do loop só é checada no fim da volta, ou seja, o corpo do loop é executado e depois a condição é checada, então o corpo do `do...while` sempre é executado pelo menos uma vez.

## Para saber mais incremento e decremento

Quando queremos incrementar o valor de uma variável inteira em uma unidade, vimos que temos 2 opções:

``` csharp
int valor = 1;

valor = valor + 1;
// ou
valor += 1;
```

Porém, como incrementar o valor de uma variável é uma atividade comum na programação, o C# nos oferece o operador `++` para realizar esse trabalho:

``` csharp
int valor = 1;
valor++;
```

Temos ainda o operador `--` que realiza o decremento de uma variável.

## Exercícios
1. Qual é o valor exibido no seguinte código:

	``` csharp
 int total = 2;
 for (int i = 0; i < 5; i += 1)
 {
    total = total * 2;
 }
 MessageBox.Show("O total é: " + total);
	```

	* 256

	* 64

	* 128

	* 512

	
1. Faça um programa em C# que imprima a soma dos números de 1 até 1000.

	
1. Faça um programa em C# que imprima todos os múltiplos de 3, entre 1 e 100.

	Para saber se um número é múltiplo de 3, você pode fazer `if(numero % 3 == 0)`.

	
1. (Opcional) Escreva um programa em C# que some todos os números de 1 a 100, pulando os múltiplos de 3.
	O programa deve imprimir o resultado final em um MessageBox.

	Qual o resultado?

	
1. (Opcional) Escreva um programa em C# que imprime todos os números que são divisíveis por 3 ou por 4 entre 0 e 30.
1. (Opcional) Faça um programa em C# que imprima os fatoriais de 1 a 10.

	O fatorial de um número n é n * n-1 * n-2 ... até n = 1.

	O fatorial de 0 é 1

	O fatorial de 1 é (0!) * 1 = 1

	O fatorial de 2 é (1!) * 2 = 2

	O fatorial de 3 é (2!) * 3 = 6

	O fatorial de 4 é (3!) * 4 = 24

	Faça um for que inicie uma variável n (número) como 1 e fatorial (resultado) como 1 e varia n de 1 até 10:

	``` csharp
 int fatorial = 1;
 for (int n = 1; n <= 10; n++)
 {

 }
	```

	
1. (Opcional) Faça um programa em C# que imprima os primeiros números da série de Fibonacci até passar de 100. A série de Fibonacci é a seguinte: 0, 1, 1, 2, 3, 5, 8, 13, 21 etc... Para calculá-la, o primeiro elemento vale 0, o segundo vale 1, daí por diante, o n-ésimo elemento vale o (n-1)-ésimo elemento somado ao (n-2)-ésimo elemento (ex: 8 = 5 + 3).

	
1. (Opcional) Faça um programa que imprima a seguinte tabela, usando `for`s encadeados:
	``` csharp
	1
	2 4
	3 6 9
	4 8 12 16
	n n*2 n*3 .... n*n
	```

	
