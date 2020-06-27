# Classes abstratas

Em nosso banco os clientes podem ter dois tipos de conta até o momento: conta corrente ou conta poupança.
Para instanciar estes tipos de conta, poderíamos usar o seguinte código:

``` csharp
//Instanciar uma nova conta corrente
ContaCorrente contaCorrente = new ContaCorrente();

//Instancia uma nova conta poupança
ContaPoupanca contaPoupanca = new ContaPoupanca();
```

Nos capítulos anteriores, aprendemos que essas duas classes têm muito em comum, ambas são **contas**. Não apenas têm atributos em comum, mas também comportamentos. Para evitar a repetição do código em ambas as classes, vimos como isolar este código repetido em uma classe `Conta`, e fazer com que as classes `ContaCorrente` e `ContaPoupanca` herdem dessa classe mãe todo o código em comum.

Além da reutilização de código, também vimos a possibilidade de escrever métodos que podem receber argumentos tanto do tipo `ContaCorrente` quanto do tipo `ContaPoupanca`, utilizando **polimorfismo**. Basta fazermos os métodos referenciarem o tipo mais genérico, no caso, `Conta`:

``` csharp
public class TotalizadorDeContas 
{
// ...
	public void Soma(Conta conta) 
	{
	// ...
	}
}
```

Mas o que acontece quando executamos a seguinte linha:
``` csharp
Conta c = new Conta();
```

Criamos um novo objeto do tipo `Conta`. Mas esse objeto faz algum sentido para nossas regras de negócio? É uma conta genérica, não sendo nem conta corrente e nem poupança.

Neste caso, não deveríamos permitir a instanciação de objetos `Conta`.

`Conta` é apenas uma ideia em nosso domínio, uma forma genérica de referenciarmos os dois tipos de conta que realmente existem em nosso sistema, `ContaCorrente` e `ContaPoupanca`. Podemos evitar a criação de objetos do tipo `Conta` definindo a classe como **abstrata**:

``` csharp
public abstract class Conta
{
//	...
}
```
Desta forma, não podemos mais criar objetos do tipo `Conta`, mas podemos ainda usar variáveis do tipo conta, para referenciar objetos de outros tipos:

``` csharp
Conta conta = new Conta(); //não compila, não pode criar objetos abstratos

Conta cc = new ContaCorrente(); // pode, objeto é do tipo ContaCorrente

Conta cp = new ContaPoupanca(); // pode, objeto é do tipo ContaPoupanca
```

Repare que o calculo necessário para realizar um saque é diferente em cada um dos tipos de `Conta`. Sabemos que uma conta deve ter um método `Saca`, mas a implementação deste método depende de regras específicas de cada tipo diferente de conta em nosso sistema. Uma solução possível seria implementá-lo sem fazer nada, mas dizendo que ele pode ser sobrescrito (`virtual`):

``` csharp
public abstract class Conta
{
	public virtual void Saca(double valor){
		//não faz nada
	}
//	...
}
```

E manter o código `Saca` original nas classes filhas, dizendo que eles sobrescrevem (`override`) o método na classe pai:

``` csharp
public class ContaCorrente : Conta
{
	public override void Saca(double valor)
	{
		this.Saldo -= (valor + 0.10);
	}
//	...
}

public class ContaPoupanca : Conta
{
	public override void Saca(double valor)
	{
		this.Saldo -= valor;
	}
//	...
}
```

Desejamos que toda classe filha implemente sua própria versão do método, com o comportamento referente ao tipo da conta. Mas se esquecermos de sobrescrever o método `Saca` em uma subclasse, o método herdado da classe `Conta` será executado, que não faz nada. Não queremos isso! Queremos obrigar as classes filha a implementar o método `Saca`.

Podemos obrigar todas as classes filhas a sobrescreverem um método na classe mãe, basta declarar esse método com o modificador `abstract` ao invés de `virtual`. Toda vez que marcamos um método com o modificador `abstract`, ele obrigatoriamente não pode ter uma implementação padrão:

``` csharp
public abstract class Conta // marcando que a classe está incompleta
{
	public abstract void Saca(double valor); // marcando que o método está incompleto
}
```

Com essa modificação, o método `Saca` passa a representar apenas uma ideia, que precisa de uma implementação concreta nas classes filhas. Caso não implementemos esse método na classe filha, o compilador emitirá um erro, avisando da obrigatoriedade de sobrescrever este método. Então se implementássemos, por exemplo, a classe `ContaPoupanca` sem definir a implementação do `Saca`, o código da classe não compilará:

``` csharp
public class ContaPoupanca : Conta
{
    // Essa classe não compila pois não colocamos a implementação para o Saca
}
```

Podemos ter uma classe abstrata sem nenhum método abstrato, mas não o contrário. Se a classe tem pelo menos um método abstrato, ela deve ser abstrata também pois como o método está incompleto, a classe não está completa.

## Exercícios
1. Transforme a classe `Conta` em uma classe abstrata. Repare que agora teremos um erro de compilação em todos os pontos do código em que tentamos instanciar o tipo `Conta`. Por quê? Modifique o código da sua aplicação para que a conta não seja instanciada, assim corrigiremos os erros de compilação. Não se esqueça de sempre testar o seu código.
1. Repare que herdamos os métodos `Saca` e `Deposita` da classe `Conta`, porém cada tipo de `Conta` sobrescreve esses métodos, logo eles são bons candidatos para métodos abstratos. Transforme os métodos `Saca` e `Deposita` em métodos abstratos, repare que com isso todas as classes filhas são obrigadas a dar uma implementação para esses métodos.
