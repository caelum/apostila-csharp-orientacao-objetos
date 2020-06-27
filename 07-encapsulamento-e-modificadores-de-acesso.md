# Encapsulamento e Modificadores de Acesso

Nesse momento, nossa classe `Conta` possui um `numero`, `saldo` e cliente `titular`, além de comportamentos que permitem sacar e depositar:

``` csharp
class Conta
{
	public int numero;
	public double saldo;
	
	public Cliente titular;
	
	public void Saca(double valor) {
		this.saldo -= valor;
	}
	
	public void Deposita(double valor) {
		this.saldo += valor;
	}
}
```

Se desejamos efetuar um saque ou um depósito em uma `Conta` qualquer, fazemos:

``` csharp
conta.Saca(100.0);
conta.Deposita(250.0);
```

Mas o que acontece se um membro da equipe faz:

``` csharp
conta.saldo -= 100.0;
```

Nada nos impede de acessar os atributos diretamente. Em três partes distintas do nosso software temos tal código:

``` csharp
// em um arquivo
conta.saldo -= 100.0;

// em outro arquivo
conta.saldo -= 250.0;

// em outro arquivo
conta.saldo -= 371.0;
```

Agora imagine que o banco mude a regra de saque: agora a cada saque realizado, o banco cobrará 0.10 centavos. Ou seja, se o usuário sacar 10.0 reais, é necessário tirar de sua conta 10.10 reais. Temos que alterar todos os pontos de nossa aplicação que acessam esse atributo! Mas nossa base de código pode ser muito grande e é muito comum esquecermos onde e quem está acessando esse atributo, deixando bugs toda vez que esquecemos de alterar algum lugar. Se tivermos essa linha espalhada 300 vezes em nosso sistema, precisaremos encontrar todas essas 300 linhas e fazer
a alteração. Muito complicado e custoso!

O que aconteceria ao usarmos o método `Saca()`:

``` csharp
// em um arquivo
conta.Saca(100.0);

// em outro arquivo
conta.Saca(250.0);

// em outro arquivo
conta.Saca(371.0);
```

Como refletiríamos a alteração na regra do saque de tirar 10 centavos? Precisamos alterar **apenas uma vez** o método `Saca()`, ao invés de alterar **todas as linhas** que acessam o atributo diretamente!

## Encapsulamento

Quando liberamos o acesso aos atributos da classe `Conta`, estamos permitindo que qualquer programador faça a sua própria implementação não segura da lógica de saque da forma que quiser. Se a modificação do atributo ficasse restrita à classe que o declara, todos que quisessem sacar ou depositar dinheiro na conta teriam de fazê-lo através de métodos da classe. Nesse caso, se a regra de saque mudar no futuro, modificaremos apenas o método Saca.

Na orientação a objetos, esconder os detalhes de implementação de uma classe é um conceito conhecido como **encapsulamento**. Como os detalhes de implementação da classe estão escondidos, todo o acesso deve ser feito através de seus métodos públicos. Não permitimos aos outros saber **COMO** a classe faz o trabalho dela, mostrando apenas
**O QUÊ** ela faz.

Veja a linha `conta.Saca(100.0);`. Sabemos o quê esse método faz pelo seu nome. Mas como ele faz o trabalho dele só saberemos se entrarmos dentro de sua implementação. Portanto, o comportamento está encapsulado nesse método.

Mas ainda não resolvemos o problema de evitar que programadores façam uso diretamente do atributo. Qualquer um ainda pode executar o código abaixo:

``` csharp
conta.saldo -= 371.0;
```

Para isso, precisamos esconder o atributo. Queremos deixá-lo privado para que somente a própria classe `Conta` possa utilizá-lo. Nesse caso queremos **modificar o acesso** ao atributo para que ele seja privado, **private**:

``` csharp
class Conta
{
	// outros atributos aqui
	private double saldo;
	
	public void Saca(double valor) {
		this.saldo -= valor;
	}
	
	public void Deposita(double valor) {
		this.saldo += valor;
	}
}
```

Atributos e métodos `private` são acessados apenas pela própria classe. Ou seja, o método `Saca()`, por exemplo, consegue fazer alterações nele. Mas outras classes não conseguem acessá-lo diretamente! O compilador não permite!

Os atributos de uma classe são detalhes de implementação, portanto marcaremos todos os atributos da conta com a palavra private:

``` csharp
class Conta
{
	private int numero;
	private double saldo;
	private Cliente titular;
	
	public void Saca(double valor) {
		this.saldo -= valor;
	}
	
	public void Deposita(double valor) {
		this.saldo += valor;
	} 
}
```

Ótimo. Agora o programador é forçado a passar pelos métodos para conseguir manipular o `saldo`. Se tentarmos, por exemplo, escrever no `saldo` da `Conta` a partir do código de um formulário, teremos um erro de compilação:

``` csharp
Conta c = new Conta();
// A linha abaixo gera um erro de compilação
c.saldo = 100.0;
```


Mas agora temos outro problema. Se quisermos exibir o saldo não conseguiremos. O `private` bloqueia tanto a escrita, quanto a leitura!

## Controlando o acesso com properties

Vimos que podemos proibir o acesso externo a um atributo utilizando o private do C#, mas o private também bloqueia a leitura do atributo, logo para recuperarmos seu valor, precisamos de um novo método dentro da classe que nos devolverá o valor atual do atributo:

``` csharp
class Conta
{
    private double saldo;
    
    private int numero;
    
    // outros atributos e métodos da conta
    
    public double PegaSaldo() 
    {
        return this.saldo;
    }
}
```

Agora para mostrarmos o `saldo` para o usuário, utilizaríamos o seguinte código:

``` csharp
Conta conta = new Conta();
// inicializa a conta

MessageBox.Show("saldo: " + conta.PegaSaldo());
```

Além disso, a conta precisa de um número, mas como ele foi declarado como `private`, não podemos acessá-lo diretamente. Precisaremos de um novo método para fazer esse trabalho:

``` csharp
class Conta
{
    private int numero;
    
    // outros atributos e métodos da conta
    
    public void ColocaNumero(int numero)
    {
        this.numero = numero;
    }
}
```

Para colocarmos o número na conta, teríamos que executar esse código:

``` csharp
Conta conta = new Conta();

conta.ColocaNumero(1100);

//utiliza a conta no código
```

Veja que com isso nós conseguimos controlar todo o acesso a classe `Conta`, mas para escrevermos ou lermos o valor de um atributo precisamos utilizar os métodos. O ideal seria utilizarmos uma sintaxe parecida com a de acesso a atributos, porém com o controle que o método nos oferece. Para resolver esse problema, o C# nos oferece as **properties** (propriedades).

A declaração de uma propriedade é parecida com a declaração de um atributo, porém precisamos falar o que deve ser feito na leitura (`get`) e na escrita (`set`) da propriedade

``` csharp
class Conta
{
    private int numero;
    
    public int Numero
    {
        get
        {
            // código para ler a propriedade
        }
        
        set
        {
            // código para escrever na propriedade
        }
    }
}
```

Na leitura da propriedade, queremos devolver o valor do atributo `numero` da `Conta`:

``` csharp
class Conta
{
    private int numero;
    
    public int Numero
    {
        get
        {
            return this.numero;
        }
    }
}
```

Com isso, podemos ler a propriedade `Numero` com o seguinte código:

``` csharp
Conta c = new Conta();
MessageBox.Show("numero: " + c.Numero);
```

Veja que o acesso ficou igual ao acesso de atributos, porém quando tentamos ler o valor de uma propriedade estamos na verdade executando um bloco de código (`get` da propriedade) da classe `Conta`. Para definirmos o número da conta, utilizaremos o código:

``` csharp
Conta c = new Conta();
c.Numero = 1;
```

Quando tentamos escrever em uma propriedade, o C# utiliza o bloco `set` para guardar seu valor. Dentro do bloco `set`, o valor que foi atribuído à propriedade fica dentro de uma variável chamada `value`, então podemos implementar o `set` da seguinte forma:

``` csharp
class Conta
{
    private int numero;
    
    public int Numero
    {
        // declaração do get
        set
        {
            this.numero = value;
        }
    }
}
```

Podemos também declarar uma propriedade que tem apenas o `get`, sem o `set`. Nesse caso, estamos declarando uma propriedade que pode ser lida mas não pode ser escrita. Com as properties conseguimos controlar completamente o acesso aos atributos da classe utilizando a sintaxe de acesso aos atributos.

## Simplificando a declaração de propriedades com Auto-Implemented Properties

Utilizando as properties, conseguimos controlar o acesso às informações da classe, porém, como vimos, declarar uma property é bem trabalhoso. Precisamos de um atributo para guardar seu valor, além disso, precisamos declarar o `get` e o `set`.

Para facilitar a declaração das properties, a partir do C# 3.0, temos as propriedades que são implementadas automaticamente pelo compilador, as **auto-implemented properties**. Para declararmos uma auto-implemented property para expor o número da conta, utilizamos o seguinte código:

``` csharp
class Conta
{
    public int Numero { get; set; }
}
```

Esse código faz com que o compilador declare um atributo do tipo `int` (cujo nome só é conhecido pelo compilador) e gere o código para a propriedade `Numero` com um `get` e um `set` que leem e escrevem no atributo declarado. Repare que ao utilizarmos as auto-implemented properties, só podemos acessar o valor do atributo declarado através da propriedade.

Toda vez que declaramos um auto-implemented property, precisamos sempre declarar um `get` e um `set` para a propriedade, porém podemos controlar a visibilidade tanto do `get` quanto do `set`. Por exemplo, no caso do saldo, queremos permitir que qualquer um leia o saldo da conta, porém apenas a própria conta pode alterá-lo. Nesse caso, utilizamos o seguinte código:

``` csharp
class Conta
{
    // outras propriedades
    
    // get é público e pode ser acessado por qualquer classe
    // set é privado e por isso só pode ser usado pela conta.
    public double Saldo { get; private set; }
    
    // resto do código da classe.
}
```

Agora vamos ver um código que tenta ler e escrever nas propriedades que declaramos:

``` csharp
Conta c = new Conta();

c.Numero = 1; // funciona pois o set do Numero é público
MessageBox.Show("numero: " + c.Numero); // funciona pois o get do Numero é público

c.Saldo = 100.0; // set do Saldo é privado, então temos um erro
MessageBox.Show("saldo " + c.Saldo); // funciona pois o get do Saldo é público.
```

Veja que tanto declarando properties explicitamente quanto utilizando as auto-implemented properties, temos o controle total sobre quais informações serão expostas pela classe.

Então devemos utilizar properties toda vez que queremos expor alguma informação da classe. Nunca devemos expor atributos da classe (utilizando o `public`), pois nunca queremos expor os detalhes de implementação da classe.

## Convenção de nome para property

A convenção de nomes definida para properties do C# é a mesma convenção de nomes utilizada para classes, ou seja, utilizando o Pascal Casing (Todas as palavras do nome são concatenadas e cada palavra tem a inicial maiúscula, por exemplo: numero do banco => `NumeroDoBanco`)

## Exercícios
1. Qual o comportamento do atributo abaixo:

	``` csharp
 public int Numero { get; private set; }
	```

	* O `número` pode ser lido, mas não pode ser alterado por outras classes.

	* O `número` não pode ser lido, mas pode ser alterado por outras classes.

	* O `número` não pode nem ser lido nem ser alterado por outras classes.

	* O `número` pode ser lido e alterado por outras classes.

	
1. Sobre o código abaixo é válido afirmar que...

	``` csharp
 Conta c = new Conta();
 double valorADepositar = 200.0;
 c.Saldo += valorADepositar;
	```

	* A operação de depósito foi implementada corretamente.

	* A operação de depósito não está encapsulada, podendo gerar problemas futuros de manutenção.

	* A operação de depósito não está encapsulada, facilitando a manutenção futura do código.

	
1. O que é encapsulamento?

	* É deixar bem claro para todos COMO a classe faz o trabalho dela.

	* É a utilização de Properties em qualquer uma de suas variações.

	* É manipular e alterar atributos diretamente, sem passar por um método específico.

	* É esconder COMO a classe/método faz sua tarefa. Caso a regra mude, temos que alterar apenas um ponto do código.

	
1. Qual o problema do atributo abaixo:

	``` csharp
 public double Saldo { get; set; }
	```

	* Nenhum. Ele está encapsulado, afinal usamos Properties.

	* Ao invés de `public`, deveríamos usar `private`.

	* O atributo `Saldo` pode ser manipulado por outras classes. Isso vai contra a regra do encapsulamento. De nada adianta criar Properties e permitir que todos os atributos sejam modificados pelas outras classes.

	
1. Transforme os atributos da classe `Conta` em propriedades. Permita que o saldo da conta seja lido, porém não seja alterado fora da classe, altere também o código das classes que utilizam a conta para que elas acessem as propriedades ao invés dos atributos diretamente.
	


## Para saber mais: Visibilidade Internal

Quando escrevemos uma aplicação grande, muitas vezes utilizamos bibliotecas que são desenvolvidas por outras pessoas, as `DLL`s (`Dynamic Link Library`). E muitas vezes a aplicação precisa compartilhar classes com a `dll` importada no código.

Quando declaramos uma classe no C#, por padrão ela só pode ser vista dentro do próprio projeto (visível apenas no `assembly` que a declarou), esse é um nível de visibilidade conhecido como **internal**. Quando queremos trabalhar com bibliotecas externas ao projeto, nossas classes precisam ser declaradas com a visibilidade `public`:

``` csharp
public class AtualizadorDeContas
{
    // Implementação da classe
}
```

Com essa modificação, a classe `AtualizadorDeContas` é visível inclusive fora do `assembly` que a declarou, ou seja, podemos utilizá-la em qualquer ponto do código.

Dentro dessa classe `AtualizadorDeContas`, vamos declarar um método chamado `Atualiza` que recebe uma `Conta` como argumento.

``` csharp
public class AtualizadorDeContas
{
    public void Atualiza(Conta conta)
    {
    
    }
}
```

Como esse é um método público dentro de uma classe pública, ele pode ser utilizado em qualquer ponto do código, inclusive em outros assemblies. Porém se a classe `Conta` for uma classe com visibilidade `internal`, teremos um método que pode ser visto em todos os pontos do código, que recebe um argumento visível apenas dentro do `assembly` que o declarou, ou seja, temos uma inconsistência nas visibilidades.

Quando o compilador do C# detecta uma inconsistência de visibilidade, ele gera um erro de compilação avisando quais são os métodos e classes que estão inconsistentes. Para corrigirmos o problema de inconsistência do exemplo do `AtualizadorDeContas`, precisamos declarar a classe `Conta` como `public`:

``` csharp
public class Conta
{
    // implementação da classe
}
```

Ou alternativamente, podemos deixar a classe `AtualizadorDeContas` ou o método `Atualiza` com visibilidade `internal`:

``` csharp
// internal é a visibilidade padrão para a classe,
// portanto a palavra internal é opcional
internal class AtualizadorDeContas
{
    // implementação da classe
}
```
