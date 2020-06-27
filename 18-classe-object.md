# Classe Object

Em capítulos anteriores vimos a utilização do polimorfismo para referenciar mais de um tipo
de classe, como é o caso das classes `ContaCorrente` e `ContaPoupanca`. Ambas podem ser
referenciadas como objetos da classe `Conta`.

Mas será que `Conta` herda de alguém? E se herdar, todas as outras classes também herdariam.
Isto é, uma classe que representa a base para todos os objetos do sistema... uma classe `Object`.
O código a seguir é o mesmo que a definição antiga de `Conta`:

``` csharp
public abstract class Conta : Object
{
  // código
}
```

É desnecessário dizermos que `Conta` herda de `Object`. É como se o próprio compilador
fizesse o código anterior ao digitarmos:

``` csharp
public abstract class Conta
{
  // código
}
```

Assim, podemos dizer que toda classe em C# é um `Object`. Uma vez que `Conta` é `Object`,
`ContaCorrente` e `ContaPoupanca` passam a ser `Object` indiretamente.

## Implementando a comparação de objetos

Vimos no primeiro capítulo sobre orientação a objetos que quando fazemos uma comparação de duas
variáveis do tipo `Conta`, o que comparamos na realidade são as referências que estão
armazenadas nas variáveis:

``` csharp
Conta c1 = new ContaCorrente();
c1.Numero = 1;

Conta c2 = new ContaCorrente();
c2.Numero = 1;

if(c1 == c2)
{
    MessageBox.Show("iguais");
}
else
{
    MessageBox.Show("diferentes");
}
```

Nesse código, as duas contas criadas guardam exatamente as mesmas informações, porém como
`c1` e `c2` guardam referências, quando fazemos `c1 == c2`, estamos comparando a referência
da variável `c1` com a referência da variável `c2` e, como elas apontam para objetos diferentes,
o código mostra a mensagem `"diferentes"`.

Para corrigir esse problema, precisamos comparar os valores das propriedades da conta ao invés do
valor das variáveis `c1` e `c2`. Por exemplo, no sistema que desenvolvemos, duas contas são
consideradas iguais apenas quando seus números são iguais, então o código da comparação deveria
ficar da seguinte forma:

``` csharp
if(c1.Numero == c2.Numero)
{
    MessageBox.Show("iguais");
}
else
{
    MessageBox.Show("diferentes");
}
```

Portanto, em todos os pontos do sistema em que precisamos comparar dois objetos do tipo conta,
precisamos repetir o `if` acima. Mas o que aconteceria se precisássemos mudar a regra de
comparação de duas contas? Nesse caso teríamos que buscar todas as comparações de contas da
aplicação e atualizar a regra, o que pode ser muito trabalhoso.

Para resolver o problema da comparação de objetos, a Microsoft introduziu na classe `Object`
um método especializado em fazer a comparação de dois objetos, o método **Equals**. Como em toda
herança a classe filha ganha os comportamentos da classe pai, podemos utilizar o `Equals` para
fazer a comparação entre dois objetos:

``` csharp
if(c1.Equals(c2))
{
    MessageBox.Show("iguais");
}
else
{
    MessageBox.Show("diferentes");
}
```

Porém, ao executarmos o código, a aplicação ainda mostra a mensagem `"diferentes"`. Isso
ocorre porque a implementação padrão do `Equals` que vem herdada da classe `Object` faz a
comparação das referências, ou seja, o `if` do código anterior ainda faz a comparação `c1 == c2`.

Podemos mudar o comportamento padrão do método `Equals` herdado da classe `Object` utilizando
a sobrescrita:

``` csharp
public abstract class Conta
{
    // outras propriedades e métodos

    // Nesse método implementamos a regra de igualdade entre duas contas
    public override bool Equals (Object outro)
    {
        // Implementação da igualdade de contas.
    }
}
```

Repare que o método `Equals` recebe um argumento do tipo `Object`, então podemos utilizá-lo para
comparar uma conta com qualquer valor do C#.

Dentro da implementação do método `Equals`, queremos implementar a regra de igualdade entre contas —
duas contas são iguais se os seus números forem iguais:

``` csharp
public abstract class Conta
{
    // outras propriedades e métodos
    
    // Nesse método implementamos a regra de igualdade entre duas contas
    public override bool Equals (Object outro)
    {
        return this.Numero == outro.Numero;
    }
}
```

Porém, repare que a variável `outro` é do tipo `Object`, que não possui uma propriedade chamada
`Numero`, apenas a `Conta` possui essa propriedade. Então, antes de fazermos a comparação
precisamos converter a variável `outro` para o tipo `Conta` utilizando o cast:

``` csharp
public abstract class Conta
{
    // outras propriedades e métodos
    
    // Nesse método implementamos a regra de igualdade entre duas contas
    public override bool Equals (Object outro)
    {
        Conta outraConta = (Conta) outro;
        return this.Numero == outraConta.Numero;
    }
}
```

Depois de colocarmos essa implementação do método `Equals` na classe `Conta`, podemos tentar
executar novamente a comparação de contas:

``` csharp
if(c1.Equals(c2))
{
    MessageBox.Show("iguais");
}
else
{
    MessageBox.Show("diferentes");
}
```

Dessa vez, o C# utilizará a implementação do `Equals` que colocamos dentro da classe `Conta`,
fazendo a comparação pelos números. Portanto, teremos a mensagem `"iguais"`.

## Melhorando a implementação do Equals com o is

Repare que o método `Equals` recebe o tipo `Object`. Sendo assim, podemos comparar a conta com
qualquer outro objeto do sistema, por exemplo, a `string`:

``` csharp
Conta c = new ContaCorrente();

if(c.Equals("Mensagem"))
```

E na implementação do `Equals`, fazemos o cast do argumento passado para o tipo `Conta`, porém
a `string` não é uma conta. Como o cast é inválido, o C# lança uma exceção do tipo
`InvalidCastException`. Para evitarmos essa exceção precisamos verificar que o argumento do
`Equals` é realmente do tipo `Conta` antes de fazermos a operação de cast. Para fazer esse
trabalho, podemos utilizar o operador **is** do C#:

``` csharp
public override bool Equals(Object outro)
{
    if(outro is Conta)
    {
        // outro é do tipo Conta, então podemos fazer o cast
    }
}
```

Nesse código, se a variável `outro` guardar uma referência para um objeto que é do tipo
`Conta` (instância de `Conta` ou classe filha), o `is` devolve `true`, se não ele
devolve `false`.

Se `outro` não for uma `Conta`, então o método deveria devolver `false`, do contrário ele deve
fazer o cast e comparar os números. Assim, o `Equals` pode ser implementado com o seguinte
código:

``` csharp
public override bool Equals(Object outro)
{
    // Se não temos um objeto do tipo Conta
    // Então o método devolve false
    if(!(outro is Conta))
    {
        return false
    }
    
    Conta outraConta = (Conta) outro;
    return this.Numero == outraConta.Numero;
}
```

## Integrando o Object com o ComboBox

Nos capítulos anteriores modificamos o formulário do `Banco` para utilizar um combo box
para fazer a organização das contas cadastradas. Para colocarmos um novo item no cambo box,
utilizamos o método `Add` em sua propriedade `Items` passando qual é o novo texto que
queremos adicionar:

``` csharp
comboContas.Items.Add("NovoItem");
```

Com isso, o combo box mostrará um novo item com o texto `"NovoItem"`. Na verdade, quando
utilizamos esse método `Add`, podemos passar qualquer objeto como argumento:

``` csharp
Conta c = new ContaCorrente();
c.Numero = 1;

comboContas.Items.Add(c);
```

Quando passamos um objeto para o método `Add`, o C# precisa transformar esse objeto em uma
`string` que será exibida como item do combo box. Para isso ele utiliza mais um método
herdado da classe `Object` chamado **ToString**. A responsabilidade desse método é
transformar um objeto qualquer em uma `string`.

A implementação padrão do método `ToString` que vem herdado da classe `Object` simplesmente
devolve a `string` que representa o nome completo da classe, ou seja, nome do namespace seguido
do nome da classe (`Banco.Contas.ContaCorrente`, no caso da `ContaCorrente`). Mas, para mostrarmos
a conta no combo box, precisamos de uma implementação que descreva a conta que o usuário está
selecionando, então vamos novamente utilizar a sobrescrita de métodos para modificar o comportamento
do `ToString`:

``` csharp
public abstract class Conta 
{
    // outros métodos e propriedades
    
    public override string ToString() 
    {
        
    }
}
```

Dentro desse método `ToString`, precisamos devolver um texto que descreva a conta que o usuário
está selecionando:

``` csharp
public abstract class Conta 
{
    // outros métodos e propriedades
    
    public Cliente Titular { get; set; }
    
    public override string ToString() 
    {
        return "titular: " + this.Titular.Nome;
    }
}
```

Agora que colocamos a implementação do `ToString` na classe `Conta`, ao executarmos novamente
o código que adiciona um item no combo box, o C# mostrará o resultado do `ToString` que foi
implementado.


## Exercícios
1. Assinale a alternativa correta

	* Todas as classes em C# herdam diretamente ou indiretamente de `Object`

	* `Object` é uma classe abstrata

	* Só as classes que não herdam de nenhuma classe são herdadas de `Object`

	* `Object` é uma interface

	
1. Analise o código a seguir e diga qual será a sua saída.

	``` csharp
 class Cliente
 {
    public string Nome { get; set; }
    public string Rg { get; set; }
    public Cliente(string nome)
    {
        this.Nome = nome;
    }
    public override bool Equals(Object obj)
    {
        Cliente outroCliente = (Cliente) obj;
        return this.Nome == outroCliente.Nome && this.Rg == outroCliente.Rg;
    }
 }
	```

	``` csharp
 Cliente guilherme = new Cliente("Guilherme Silveira");
 guilherme.Rg = "12345678-9";

 Cliente mauricio = new Cliente("Mauricio Aniche");
 mauricio.Rg = "12345678-9";

 if (guilherme.Equals(mauricio))
 {
    MessageBox.Show("São o mesmo cliente");
 }
 else
 {
    MessageBox.Show("Não são o mesmo cliente");
 }
	```

	* Não são o mesmo cliente

	* O código não compila

	* São o mesmo cliente

	* Nada é mostrado

	* O código roda mas quebra ao executar

	
1. Vamos sobrescrever o método `ToString` da classe Conta com a seguinte implementação:

	``` csharp
 public abstract class Conta 
 {
    // Resto da implementação da Conta
    public override String ToString()
    {
        return "titular: " + this.Titular.Nome;
    }
 }
	```

	Agora adicionaremos a conta ao invés de uma `string` como item do combo box dentro do
	método `AdicionaConta` do formulário principal da aplicação, classe `Form1`:

	``` csharp
 public void AdicionaConta(Conta conta)
 {
    this.contas[this.numeroDeContas] = conta;
    this.numeroDeContas++;
    comboContas.Items.Add(conta);
 }
	```

	Depois de fazer essa modificação, teste a aplicação e veja o `ToString` da conta em ação
	dentro dos opções do combo box.
1. Quando adicionamos um objeto no combo box, é mais interessante recuperar diretamente o objeto
	que foi selecionado do que o índice que foi selecionado.

	Para recuperar o objeto que está selecionado em um combo box, utilizamos a propriedade `SelectedItem`.
	Essa propriedade devolve um `Object` que guarda a instância selecionada no combo box.

	Sabendo disso, podemos modificar a ação do botão de depósito, `botaoDeposito_Click` da classe `Form1`,
	para utilizar o `SelectedItem` do `comboContas`, que conterá a instância da conta que o usuário
	selecionou na interface gráfica. Porém para podermos utilizar a conta selecionada, precisamos primeiro
	convertê-la para uma instância de `Conta`:

	``` csharp
 private void botaoDeposito_Click(object sender, EventArgs e)
 {
    Conta selecionada = (Conta) comboContas.SelectedItem;

    // implementa a lógica de depósito utilizando a conta
 }
	```

	Implemente e teste essa modificação dentro do seu projeto. Faça o mesmo para o botão de saque.
1. (Opcional) Em algumas situações não queremos utilizar o `ToString` do próprio objeto
	para montar a lista de itens do combo box, nessas situações, podemos utilizar uma
	propriedade do `ComboBox` chamada `DisplayMember` para escolher qual é a propriedade
	do objeto que queremos incluir como item do combo. Por exemplo, no seguinte código, os
	items do combo box serão 1 e 2:

	``` csharp
 Conta c = new ContaCorrente() { Numero = 1 };
 Conta c2 = new ContaCorrente() { Numero = 2 };
 comboContas.Items.Add(c);
 comboContas.Items.Add(c2);
 comboContas.DisplayMember = "Numero";
	```

	Quando utilizamos o `DisplayMember` o combo box também utiliza o `ToString` do membro
	para montar o item que será exibido para o usuário.

	Utilize o `DisplayMember` para mostrar o `ToString` da propriedade `Titular` da conta
	ao invés de mostrar o `ToString` da própria `Conta`.
