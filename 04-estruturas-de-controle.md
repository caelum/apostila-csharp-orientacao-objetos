# Estruturas de controle

## Tomando decisões no código

Voltando para nosso exemplo de aplicação bancária, queremos permitir um saque somente se o valor a ser retirado for menor ou igual ao saldo da conta, ou seja, se o saldo da conta for maior ou igual ao valor do saque, devemos permitir a operação, do contrário não podemos permitir o saque. Precisamos fazer execução condicional de código.

No C#, podemos executar código condicional utilizando a construção `if`:

``` csharp
if (condicao) 
{
    // Esse código será executado somente se a condição for verdadeira
}
```

No nosso exemplo, queremos executar a lógica de saque apenas se o saldo for maior ou igual ao valor do saque:

``` csharp
double saldo = 100.0;
double valorSaque = 10.0;
if (saldo >= valorSaque) 
{
    // código do saque.
}
```

O código do saque deve diminuir o saldo da conta e mostrar uma mensagem para o usuário indicando que o saque ocorreu com sucesso:

``` csharp
double saldo = 100.0;
double valorSaque = 10.0;
if (saldo >= valorSaque) 
{
    saldo = saldo - valorSaque;
    MessageBox.Show("Saque realizado com sucesso");
}
```

Repare que, se a conta não tiver saldo suficiente para o saque, o usuário não é avisado. Então estamos na seguinte situação: "Se a conta tiver saldo suficiente, quero fazer o saque, senão, quero mostrar a mensagem Saldo Insuficiente para o usuário". Para fazer isso, podemos usar o `else` do C#:

``` csharp
if (saldo >= valorSaque) 
{
    // código do saque
}
else
{
    MessageBox.Show("Saldo Insuficiente");
}
```

## Mais sobre condições

Repare na expressão que passamos para o `if`: `saldo >= valorSaque`. Nele, utilizamos o operador "maior ou igual". Além dele, existem outros operadores de comparação que podemos utilizar: maior (>), menor (<), menor ou igual (<=), igual (==) e diferente (!=). Podemos também negar uma condição de um `if` utilizando o operador `!` na frente da condição que será negada.

No capítulo anterior, vimos que um valor tem um tipo associado em C#: `10` é um `int`, `"mensagem"` é uma `string`. Da mesma forma, a expressão `saldo >= valorSaque` também tem um tipo associado: o tipo `bool`, que pode assumir os valores `true` (verdadeiro) ou `false` (falso). Podemos inclusive guardar um valor desse tipo numa variável:

``` csharp
bool podeSacar = (saldo >= valorSaque);
```

Também podemos realizar algumas operações com valores do tipo `bool`. Podemos, por exemplo, verificar se duas condições são verdadeiras ao mesmo tempo usando o operador `&&` (AND) para fazer um e lógico:

``` csharp
bool realmentePodeSacar = (saldo >= valorSaque) && (valorSaque > 0);
```

Quando precisamos de um OU lógico, utilizamos o operador `||`:

``` csharp
// essa condição é verdadeira se (saldo >= valorSaque) for true
// ou se (valorSaque > 0) for verdadeiro.
bool realmentePodeSacar = (saldo >= valorSaque) || (valorSaque > 0);
```

Assim, podemos construir condições mais complexas para um `if`. Por exemplo, podemos usar a variável `realmentePodeSacar` declarada no `if` que verifica se o cliente pode sacar ou não:

``` csharp
if (realmentePodeSacar) 
{
    // código do saque
}
else
{
    MessageBox.Show("Saldo Insuficiente");
}
```

## Exercícios opcionais
1. Qual é a mensagem e o valor da variável saldo após a execução do seguinte código?
	``` csharp
 double saldo = 100.0;
 double valorSaque = 10.0;
 if (saldo >= valorSaque)
 {
    saldo -= valorSaque;
    MessageBox.Show("Saque realizado com sucesso");
 }
 else
 {
    MessageBox.Show("Saldo Insuficiente");
 }
	```

	* mensagem: Saque realizado com sucesso; saldo: 90.0

	* mensagem: Saldo Insuficiente; saldo 90.0

	* mensagem: Saque realizado com sucesso; saldo: 100.0

	* mensagem: Saldo Insuficiente; saldo 100.0

	* mensagem: Saque realizado com sucesso; saldo: 10.0

	
1. Qual é a mensagem e o valor da variável saldo após a execução do seguinte código?

	``` csharp
 double saldo = 5.0;
 double valorSaque = 10.0;
 if (saldo >= valorSaque)
 {
    saldo -= valorSaque;
    MessageBox.Show("Saque realizado com sucesso");
 }
 else
 {
    MessageBox.Show("Saldo Insuficiente");
 }
	```

	* mensagem: Saque realizado com sucesso; saldo: -5.0

	* mensagem: Saldo Insuficiente; saldo -5.0

	* mensagem: Saque realizado com sucesso; saldo: 5.0

	* mensagem: Saldo Insuficiente; saldo 5.0

	* mensagem: Saque realizado com sucesso; saldo: 10.0

	
1. Em alguns casos, podemos ter mais de duas decisões possíveis. O banco pode, por exemplo, decidir que contas com saldo menor que R$ 1000 pagam 1% de taxa de manutenção, contas com saldo entre R$ 1000 e R$ 5000 pagam 5% e contas com saldo maior que R$ 5000 pagam 10%.

	Para representar esse tipo de situação, podemos usar o `else if` do C#, que funciona em conjunto com o `if` que já conhecemos. Veja como ficaria a situação descrita anteriormente:

	``` csharp
 double taxa;
 if (saldo < 1000)
 {
    taxa = 0.01;
 }
 else if (saldo <= 5000)
 {
    taxa = 0.05;
 }
 else
 {
    taxa = 0.1;
 }
	```

	O C# vai processar as condições na ordem, até encontrar uma que seja satisfeita. Ou seja, na segunda condição do código, só precisamos verificar que `saldo` é menor ou igual a R$ 5000, pois se o C# chegar nessa condição é porque ele não entrou no primeiro `if`, isto é, sabemos que o `saldo` é maior ou igual a R$ 1000 nesse ponto.

	Com base nisso, qual vai ser a mensagem exibida pelo código seguinte?

	``` csharp
 double saldo = 500.0;
 if (saldo < 0.0)
 {
    MessageBox.Show("Você está no negativo!");
 }
 else if (saldo < 1000000.0)
 {
    MessageBox.Show("Você é um bom cliente");
 }
 else
 {
    MessageBox.Show("Você é milionário!");
 }
	```

	* "Você está no negativo!"

	* "Você é um bom cliente"

	* Nenhuma mensagem

	* "Você é milionário!"

	* "Você é um bom cliente", seguida de "Você é milionário!"

	
1. Uma pessoa só pode votar em eleições brasileiras se ela for maior que 16 anos e for cidadã brasileira. Crie um programa com duas variáveis, `int idade, bool brasileira`, e faça com que o programa diga se a pessoa está apta a votar ou não, de acordo com os dados nas variáveis.

	
1. Crie um programa que tenha uma variável `double valorDaNotaFiscal` e, de acordo com esse valor, o imposto deve ser calculado. As regras de cálculo são:

	* Se o valor for menor ou igual a 999, o imposto deve ser de 2%
	* Se o valor estiver entre 1000 e 2999, o imposto deve ser de 2.5%
	* Se o valor estiver entre 3000 e 6999, o imposto deve ser de 2.8%
	* Se for maior ou igual a 7000, o imposto deve ser de 3%

	Imprima o imposto em um MessageBox.

1. (Desafio)Dado o seguinte código:
	``` csharp
 int valor = 15;
 string mensagem = "";
 if(valor > 10)
 {
    mensagem = "Maior que dez";
 }
 else
 {
    mensagem = "Menor que dez;
 }
 MessageBox.Show(mensagem);
	```
	Existe uma forma de fazer o if desse código uma **linha só**, sem usar a palavra if e else.
	Pesquise sobre isso e tente fazer.
