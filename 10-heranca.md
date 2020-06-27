# Herança

Imagine agora que nosso banco realize depósitos e saques de acordo com o tipo da conta. Se a conta for poupança, o cliente deve pagar 0.10 por saque. Se a conta for corrente, não há taxa.

Para implementar essa regra de negócio, vamos colocar um `if` no método `Saca`:

``` csharp
public void Saca(double valor) 
{
	if(this.Tipo == ???????????) 
	{
		this.Saldo -= valor + 0.10;
	}
	else 
	{
		this.Saldo -= valor;		
	}
}
```

Podemos criar um atributo na `Conta`, que especifica o tipo da conta como, por exemplo, um inteiro qualquer onde o número 1 representaria "conta poupança" e 2 "conta corrente".

A implementação seria algo como:

``` csharp
public class Conta
{
	public int Numero { get; set;}
	public double Saldo { get; private set; }

	public Cliente Titular { get; set; }
	
	public int Tipo { get; set; }
	
	public void Saca(double valor) 
	{
		if(this.Tipo == 1) 
		{
			this.Saldo -= valor + 0.10;
		}
		else 
		{
			this.Saldo -= valor;		
		}
	}
	
	public void Deposita(double valor) 
	{
		this.Saldo += valor;
	}
}
```

Veja que uma simples regra de negócio como essa fez nosso código crescer muito. E poderia ser pior: imagine se nosso banco tivesse 10 tipos de contas diferentes. Esse `if` seria maior ainda.

Precisamos encontrar uma maneira de fazer com que a criação de novos tipos de conta não implique em um aumento de complexidade.

## Reaproveitando código com a Herança

Uma solução seria ter classes separadas para `Conta` (que é a corrente) e `ContaPoupanca`:

``` csharp
public class Conta
{
	public int Numero { get; set;}
	public double Saldo { get; private set; }
	
	public Cliente Titular { get; set; }
	
	public void Saca(double valor) 
	{
		this.Saldo -= valor;
	}
	
	public void Deposita(double valor) 
	{
		this.Saldo += valor;
	}
}

public class ContaPoupanca
{
	public int Numero { get; set;}
	public double Saldo { get; private set; }

	public Cliente Titular { get; set; }
	
	public void Saca(double valor) 
	{
		this.Saldo -= (valor + 0.10);
	}
	
	public void Deposita(double valor) 
	{
		this.Saldo += valor;
	}
}
```

Ambas as classes possuem código bem simples, mas agora o problema é outro: a repetição de código entre ambas as classes. Se amanhã precisarmos guardar "CPF", por exemplo, precisaremos mexer em todas as classes que representam uma conta no sistema. Isso pode ser trabalhoso.

A ideia é, portanto, reaproveitar código. Veja que, no fim, uma `ContaPoupanca` é uma `Conta`, pois ambos tem `Numero`, `Saldo` e `Titular`. A única diferença é o comportamento no momento do saque. Podemos falar que uma `ContaPoupanca` é uma `Conta`:

``` csharp
public class ContaPoupanca : Conta 
{

}
```

Quando uma classe é definida com o **:**, dizemos que ela herda da outra (`Conta`) e por isso ela ganha todos os atributos e métodos da outra classe. Por exemplo, se `ContaPoupanca` herdar de `Conta`, isso quer dizer que ela terá `Numero`, `Saldo`, `Titular`, `Saca()` e `Deposita()` automaticamente, sem precisar fazer nada. Dizemos que a classe `ContaPoupanca` é uma **subclasse** ou **classe filha** da classe `Conta` e que `Conta` é uma **classe base** ou **classe pai** da `ContaPoupanca`. Veja o código a seguir:

``` csharp
// Arquivo ContaPoupanca.cs
public class ContaPoupanca : Conta 
{

}

// Código no formulário que utiliza a ContaPoupanca
ContaPoupanca c = new ContaPoupanca();
c.Deposita(100.0);
```

Basta usar a notação `:` e o C# automaticamente herda os métodos e atributos da classe pai. Mas a `ContaPoupanca` tem o comportamento de `Saca()` diferente. Para isso, basta reescrever o comportamento na classe filha, usando a palavra `override` e mudando a classe pai para indicar que o método pode ser sobrescrito (`virtual`):

``` csharp
// Arquivo Conta.cs
public class Conta
{
	public virtual void Saca(double valor) 
	{
		this.Saldo -= valor;
	}
    
    // Resto do código da classe
}

// Arquivo ContaPoupanca.cs
public class ContaPoupanca : Conta 
{
	public override void Saca(double valor) 
	{
		this.Saldo -= (valor + 0.10);
	}
}

// Código do formulário da aplicação
ContaPoupanca c = new ContaPoupanca();

// chama o comportamento escrito no pai
// O Saldo termina em 100.0 depois dessa linha
c.Deposita(100.0); 

// chama o comportamento escrito na ContaPoupanca
// O Saldo termina com o valor 49.90
c.Saca(50); 
```

Veja nesse código que invocamos tanto `Deposita()` quanto `Saca()`. No depósito, como a classe filha não redefiniu o comportamento, o método escrito na classe pai será utilizado.

Já no saque, o comportamento usado é o que foi sobrescrito na classe filha.

Mas o código anterior ainda não compila. Repare que o método `Saca()` da `ContaPoupanca` manipula o `Saldo`. Mas `Saldo` é privado! Atributos privados só são visíveis para a classe que os declarou. Os filhos não enxergam.

Queremos proteger nosso atributo mas não deixá-lo privado nem público. Queremos proteger o suficiente para ninguém de fora acessar, mas apenas quem herda ter acesso. Para resolver, alteraremos o modificador de acesso para **protected**. Atributos/métodos marcados como `protected` são visíveis apenas para a própria classe e para as classes filhas:

``` csharp
public class Conta
{
	public int Numero { get; set;}
	public double Saldo { get; protected set; }

	// ...
}
```

A classe `Conta` ainda pode ser instanciada sem problemas:

``` csharp
Conta c = new Conta();
c.Deposita(100.0);
```

Veja que com herança conseguimos simplificar e reutilizar código ao mesmo tempo. A herança é um mecanismo poderoso mas deve ser utilizado com cuidado.

## Reaproveitando a implementação da classe base

Observe as implementações dos métodos `Saca` das classes `Conta` e `ContaPoupanca`:

``` csharp
public class Conta
{
    // outros atributos e métodos
    public double Saldo { get; protected set; }
    
    public virtual void Saca(double valor)
    {
        this.Saldo -= valor;
    }
}

public class ContaPoupanca : Conta
{
    public override void Saca(double valor)
    {
        this.Saldo -= (valor + 0.10);
    }
}
```

As implementações dos dois métodos são praticamente iguais, a única diferença é que no `Saca` da `ContaPoupanca` colocamos `this.Saldo -= (valor + 0.10);` ao invés de `this.Saldo -= valor;`.

Quando fazemos a sobrescrita de métodos em uma classe filha, muitas vezes, queremos apenas mudar levemente o comportamento da classe base. Nessas situações, no código da classe filha, podemos reutilizar o código da classe pai com a palavra **base** chamando o comportamento que queremos reaproveitar. Então o código do `Saca` da `ContaPoupanca` poderia ser reescrito da seguinte forma:

``` csharp
public class ContaPoupanca : Conta
{
    public override void Saca(double valor)
    {
        base.Saca(valor + 0.10);
    }
}
```

Com essa implementação, o `Saca` da `ContaPoupanca` chama o método da classe base passando como argumento `valor + 0.10`. Repare também que, como a classe filha não está utilizando a propriedade `Saldo` da `Conta`, ela poderia voltar a ser `private`:

``` csharp
public class Conta
{
    public double Saldo { get; private set; }
    
    // outras propriedades e métodos
}
```

## Polimorfismo

Nosso banco tem relatórios internos para saber como está a saúde financeira da instituição, além de relatórios sobre os clientes e contas. Em um deles, é necessário calcular a soma do saldo de todas as contas (correntes, poupanças, entre outras) que existem no banco. Começando com "zero reais":

``` csharp
public class TotalizadorDeContas 
{
	public double ValorTotal { get; private set; }	
}
```

Permitimos adicionar `Conta` ao nosso relatório e acumular seu saldo:

``` csharp
public class TotalizadorDeContas 
{
	public double ValorTotal { get; private set; }
	
	public void Soma(Conta conta) 
	{
		ValorTotal += conta.Saldo;
	}
}

Conta c1 = new Conta();
Conta c2 = new Conta();

TotalizadorDeContas t = new TotalizadorDeContas();
t.Soma(c1);
t.Soma(c2);
```

Ótimo. Mas o problema é que temos classes que representam diferentes tipos de contas, e queremos acumular o saldo delas também. Uma primeira solução seria ter um método `Soma()` para cada classe específica:

``` csharp
public class TotalizadorDeContas 
{
	public double ValorTotal { get; private set; }
	
	public void Soma(Conta conta) { /* ... */ }
	public void Soma(ContaPoupanca conta) { /* ... */ }
	public void Soma(ContaEstudante conta) { /* ... */ }
	// mais um monte de métodos aqui!
}
```

Novamente caímos no problema da repetição de código.

Veja que `ContaPoupanca` é uma `Conta`. Isso é inclusive expresso através da relação de herança entre as classes. E, já que `ContaPoupanca` é uma `Conta`, o C# permite que você passe `ContaPoupanca` em lugares que aceitam referências do tipo `Conta`! Linguagens orientadas a objetos, como C#, possuem essa solução elegante pra isso.

Veja o código a seguir:

``` csharp
public class TotalizadorDeContas 
{
	public double ValorTotal { get; private set; }
	
	public void Soma(Conta conta) 
	{
		ValorTotal += conta.Saldo;
	}
}

Conta c1 = new Conta();
ContaPoupanca c2 = new ContaPoupanca();

TotalizadorDeContas t = new TotalizadorDeContas();
t.Soma(c1);
t.Soma(c2); // funciona!
```

O código funciona! Podemos passar `c2` ali para o método `Soma()`.

Mas como isso funciona? O C# sabe que `ContaPoupanca` herda todos os atributos e métodos de `Conta`, e portanto, tem a certeza de que existe o atributo `Saldo`, e que ele poderá invocá-lo sem maiores problemas!

Agora, uma `ContaPoupanca` tem um novo comportamento, que permite calcular seus rendimentos. Para isso o desenvolvedor criou um comportamento chamado `CalculaRendimento()`:

``` csharp
class ContaPoupanca : Conta 
{
	public void CalculaRendimento()
	{
		// ...
	}
}
```

Veja o método `Soma()`. Ele invoca também o `CalculaRendimento()`:

``` csharp
public class TotalizadorDeContas 
{
	public double ValorTotal { get; private set; }
	
	public void Soma(Conta conta) 
	{
		ValorTotal += conta.Saldo;
		conta.CalculaRendimento(); // não compila!
	}
}
```

O código anterior não compila. Por quê? Porque o C# não consegue garantir que o que virá na variável `conta` será uma `ContaPoupanca`. Naquela "porta" entra `Conta` ou qualquer filho de `Conta`. Portanto, tudo o que o C# consegue garantir é que o objeto que entrar ali tem tudo que `Conta` tem. Por isso, só podemos usar métodos e atributos definidos pelo tipo da variável (no caso, `Conta`.)

Essa ideia de uma variavel conseguir referenciar seu próprio tipo ou filhos desse tipo é conhecido por **polimorfismo**. Veja que, com o uso de polimorfismo, garantimos que a classe `TotalizadorDeContas` funcionará para todo novo tipo de `Conta` que aparecer.

Se no futuro um novo tipo de conta, como conta investimento, for criada, basta que ela herde de `Conta` e não precisaremos nunca modificar essa classe! Ela funcionará naturalmente!

Um programador que conhece bem orientação a objetos faz uso constante de polimorfismo. Veremos mais pra frente como continuar usando polimorfismo para escrever código de qualidade, tomando cuidado para não abusar dessa ideia.

## Exercícios
1. Qual a diferença entre `private` e `protected`?

	* Nenhuma. Em ambos, o atributo/método é visível para todos.

	* Só a própria classe enxerga atributos/métodos `private` enquanto `protected` é visto pela própria classe mais as classes filhas.

	* Só a própria classe enxerga atributos/métodos `protected` enquanto `private` é visto pela própria classe mais as classes filhas.

	
1. Para que serve a palavra `override`?

	* Para indicar que o método está sobrescrevendo um método da classe pai.

	* Para não permitir acesso ao atributo por outras classes.

	* Para indicar que esse método não deve ser utilizado.

	
1. Pra que serve a palavra `virtual`?

	* Para permitir que o método seja sobrescrito.

	* Para indicar que esse método não deve ser sobrescrito.

	* Para sobrescrever um método da classe pai.

	* Para não permitir acesso ao atributo por outras classes.

	
1. Adicione a classe `ContaPoupanca` na aplicação. Essa classe deve herdar da `Conta` e sobrescrever o comportamento do método `Saca` para que todos os saques realizados na conta poupança paguem uma taxa de R$ 0.10.

	Não se esqueça de utilizar as palavras `virtual` e `override` para sobrescrever os métodos.
	
1. O que acontece ao executarmos o código a seguir:

	``` csharp
 Conta c1 = new ContaPoupanca();
 c1.Deposita(100.0);
 c1.Saca(50.0);
 MessageBox.Show("conta poupança = " + c1.Saldo);

 Conta c2 = new Conta();
 c2.Deposita(100.0);
 c2.Saca(50.0);
 MessageBox.Show("conta = " + c2.Saldo);
	```

	* conta poupanca = 49.9 e conta = 49.9

	* conta poupança = 49.9 e conta = 50.0

	* conta poupança = 50.0 e conta = 50.0

	* conta poupança =  50.0 e conta = 49.9

	
1. Faça com que o método `Form1_Load`, instancie uma `ContaPoupanca` ao invés da `Conta`:

	``` csharp
 public partial class Form1 : Form
 {
    // Essa é a mesma declaração que colocamos no capítulo anterior
	private Conta conta;
	private void Form1_Load(object sender, EventArgs e)
	{
		this.conta = new ContaPoupanca();
		// resto do código continua igual
	}

	// código do resto do formulário também continua igual
 }
	```

	Repare que não precisamos modificar o tipo da variável conta, pois como a `ContaPoupanca` herda de `Conta`, podemos utilizar o polimorfismo para atribuir uma referência do tipo `ContaPoupanca` em uma variável do tipo `Conta`.

	Depois de modificar o programa, execute-o e teste a operação de depósito. Repare que na classe `ContaPoupanca` não declaramos o método `Deposita`, mas mesmo assim conseguimos invocá-lo dentro do código, isso é possível pois quando utilizamos herança, todo o comportamento da classe pai é herdado pela classe filha.

	Teste também o botão de saque. Como a `ContaPoupanca` sobrescreve o método `Saca` da `Conta`, ao apertarmos o botão de saque, estamos invocando o comportamento especializado que foi implementado na `ContaPoupanca` ao invés de usar o que foi herdado da `Conta`.
1. Crie a classe `ContaCorrente` dentro do projeto e faça com que ela herde da classe `Conta`.

	Todas as operações na `ContaCorrente` serão tarifadas, em todo Saque, precisamos pagar uma taxa de R$ 0.05 e para os depósitos, R$ 0.10, ou seja, na `ContaCorrente`, precisaremos sobrescrever tanto o método `Saca` quanto o `Deposita`. Não se esqueça de usar o `virtual` e `override` para fazer a sobrescrita no código.

	Depois de criar a `ContaCorrente`, modifique novamente o formulário para que ele mostre as informações de uma `ContaCorrente` ao invés de uma `ContaPoupanca`.
1. (Opcional) Implemente a classe `TotalizadorDeContas` com uma propriedade chamada `SaldoTotal` e um método chamado `Adiciona` que deve receber uma conta e somar seu saldo ao saldo total do totalizador. Escreva um código que testa o totalizador.

	Podemos passar uma instância de `ContaCorrente` para o `Adiciona` do totalizador? Por quê?

	



## Para saber mais — o que é herdado?

Neste capítulo, vimos que quando fazemos a classe `ContaPoupanca` herdar da classe `Conta`, ela recebe automaticamente todos os atributos, propriedades e métodos da classe pai, porém os construtores da classe pai não são herdados. Então se a classe filha precisa de um construtor que está na classe pai, ela deve explicitamente declarar esse construtor em seu código.

Imagine por exemplo, que para construirmos a conta precisamos passar opcionalmente seu número:

``` csharp
public class Conta
{
    public int Numero { get; set; }
    // Construtor sem argumentos
    public Conta() {}
    
    public Conta(int numero)
    {
        this.Numero = numero;
    }
}
```
Agora na classe `ContaPoupanca` queremos passar o número na construção do objeto, como o construtor não é herdado, precisamos colocar a declaração explicitamente:

``` csharp
public class ContaPoupanca : Conta
{
    public ContaPoupanca(int numero)
    {
        // A propriedade Numero veio herdada da classe Conta
        this.Numero = numero;
    }
}
```

Nesse construtor que acabamos de declarar na classe `ContaPoupanca`, fizemos apenas a inicialização da propriedade número, exatamente o mesmo código que temos no construtor da classe pai, então ao invés de repetirmos o código, podemos simplesmente chamar o construtor que foi declarado na classe `Conta` a partir do construtor da classe `ContaPoupanca` utilizando a palavra **base**:

``` csharp
public class ContaPoupanca : Conta
{
    // Estamos chamando o construtor da classe pai que já faz a inicialização
    // do número e por isso o corpo do construtor pode ficar vazio.
    public ContaPoupanca(int numero) : base (numero) { }
}
```

Na verdade, dentro do C#, sempre que construímos uma instância de `ContaPoupanca`, o C# sempre precisa chamar um construtor da classe `Conta` para fazer a inicialização da classe base. Quando não invocamos explicitamente o construtor da classe pai, o C# coloca implicitamente uma chamada para o construtor sem argumentos da classe pai:

``` csharp
public class ContaPoupanca : Conta
{
    // nesse código o c# chamará o construtor sem argumentos da classe Conta.
    public ContaPoupanca(int numero)
    {
        this.Numero = numero;
    }
}
```

Se a classe `Conta` não definir o construtor sem argumentos, temos um erro de compilação se não invocarmos explicitamente um construtor da classe pai.
