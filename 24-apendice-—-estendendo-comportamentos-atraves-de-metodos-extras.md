# Apêndice — estendendo comportamentos através de métodos extras

Muitas vezes usamos classes criadas por outros desenvolvedores, como por exemplo todas as classes do .NET framework. A classe `string` é um bom exemplo e cheia de métodos úteis mas quem desenhou a classe não colocou um método para transformar uma palavra em seu plural, por exemplo. O que fazer se queremos o plural de "conta" e "banco" gerado automaticamente?

Uma abordagem é a criação de um método estático que pode ser chamado:

``` csharp
public static class StringUtil
{
  public static string Pluralize(string texto)
  {
    if(texto.EndsWith("s"))
    {
      return texto;
    }
    else
    {
      return texto + "s";
    }
  }
}
```

Claro que esse método é uma abordagem bem simples para um algoritmo que é capaz de retornar plurais, mas já resolve o problema no caso geral. Agora podemos em todo lugar do nosso código fazer:

``` csharp
string bancos = StringUtil.Pluralize("banco");
string contas = StringUtil.Pluralize("conta");
```

Por mais que a implementação funcione, o código fica muito feio porque toda vez precisamos invocar o método estático. Não seria possível estender a classe `string` para fazer algo como o código a seguir?

``` csharp
String texto = "banco";
String plural = texto.Pluralize();
```

O C# permite a criação de métodos de extensão para classes que já existem através do uso da palavra `using`, mas para isso devemos colocar nossa classe estática dentro de um namespace e adicionar a palavra `this` ao primeiro parâmetro:

``` csharp
namespace MinhasExtensoes {
  public static class StringExtensions
  {
    public static string Pluralize(this string texto)
    {
      if(texto.EndsWith("s"))
      {
        return texto;
      }
      else
      {
        return texto + "s";
      }
    }
  }
}
```

Agora podemos:

``` csharp
using MinhasExtensoes;

string texto = "banco";
string plural = texto.Pluralize();
```

Note como, ao importar as extensões, todos os métodos estáticos de classes estáticas públicas dentro do namespace importado estarão disponíveis para serem acessados como se pertencessem a classe, apesar de não pertencerem.

É importante lembrar que o método só pode ser acessado caso ainda não exista um outro método com o mesmo nome e tipos de parâmetros na classe. Isto é, não seria possível estender a classe `string` com um novo método `ToString()` pois ele já existe. Só podemos adicionar novos comportamentos.

Exatamente por isso pode ser perigoso adicionar métodos como extensões sem cuidado nenhum: no futuro alguém pode adicionar esse método à classe que estendemos e agora nosso código quebra pois não é mais compatível. Somente estenda os comportamentos de uma classe caso seja necessário.

## Exercícios
1. Queremos "adicionar" a todas as nossas contas a capacidade de uma Conta se transformar em XML. Para isso o C# já dispõe uma API:

	``` csharp
 using System.IO;
 using System.Xml.Serialization;
 public static class Serializer {
    public static string AsXml(Conta resource)
    {

        var stringWriter = new StringWriter();
        new XmlSerializer(resource.GetType()).Serialize(stringWriter, resource);
        return stringWriter.ToString();
    }
 }
	```

	Para acessar esse processo fazemos:

	``` csharp
 Conta conta = new Conta();
 System.Console.Write(Serializer.AsXml(conta));
	```

	Como desejamos usar o recurso de extensão externa do C# para poder "adicionar" um método a todos as contas do sistema, o que devemos colocar na linha comentada para definir nosso método?

	``` csharp
 using System.IO;
 using System.Xml.Serialization;

 namespace Caelum {
    public static class ObjectExtensions
    {
        // Definição do método
        {
            var stringWriter = new StringWriter();
            new XmlSerializer(resource.GetType()).Serialize(stringWriter, resource);
            return stringWriter.ToString();
        }
    }
 }
	```

	``` csharp
 // Uso
 using System;
 using Caelum;
 Conta conta = new Conta();
 Console.Write(conta.AsXml());
	```

	* public static string AsXml(this Conta resource)

	* public string AsXml(Conta resource)

	* public string AsXml(this Conta resource)

	* public static string AsXml(Conta resource)

	* public static extension string AsXml(Conta resource)

	* public static string AsXml(extension Conta resource)

	
1. Em vez de adicionar o extension method a todas as nossas contas, queremos incluir esse comportamento como extensão a todos os objetos do sistema. Como definir esse método?

	``` csharp
 public static class Serializer {
  // como definir o método???
  {

    var stringWriter = new StringWriter();
    new XmlSerializer(resource.GetType()).Serialize(stringWriter, resource);
    return stringWriter.ToString();

  }
 }
	```

	* public static string AsXml(this object resource)

	* public static string AsXml(this resource)

	* public static string AsXml(object resource)

	* public static string AsXml(this all resource)

	
1. Definimos uma extensão a `object` da seguinte maneira:

	``` csharp
	namespace Caelum
	{
	public static class ObjectExtensions
	{
	public static string ToString(this object resource)
	{
	var stringWriter = new StringWriter();
	new XmlSerializer(resource.GetType()).Serialize(stringWriter, resource);
	return stringWriter.ToString();
	}
	}
	}
	```

 Ao definir o `using` adequado, qual o resultado ao chamar o `ToString` a seguir?

	``` csharp
	using Caelum;
	Conta conta = new Conta();
	MessageBox.Show(conta.ToString());
	```

 * Não compila pois não podemos sobrescrever o método ToString.

 * Mostra uma versão em xml de nossa conta, ou seja, o C# usa o extension method.

 * Imprime o resultado tradicional do método ToString, ou seja, o C# não usa o extension method.

 
1. Dada a classe Conta definida nos capítulos anteriores:
	``` csharp
	public abstract class Conta
	{
	public double Saldo { get; protected set; }

	// outros métodos e propriedades da classe Conta
	}
	```

 E uma classe com um extension method para a conta:
	``` csharp
	public static class ContaExtensions
	{
	public static void MudaSaldo (this Conta conta, double novoSaldo)
	{
	conta.Saldo = novoSaldo;
	}
	}
	```

 Escolha a alternativa com a afirmação verdadeira.

 * Esse código não compila, pois o Extension Method só pode acessar a interface pública da Conta.

 * O código funciona normalmente, pois o compilador do C# trata um extension method como se fosse um método da Conta e, portanto, o método pode acessar os métodos e atributos private e protected da Conta.

 * O código compila normalmente, porém só podemos usar o MudaSaldo dentro da própria classe Conta.

 
1. Sobre o código a seguir:

	``` csharp
	public abstract class Conta
	{
	public Cliente Titular { get; set; }
	// outros métodos e atributos da conta
	}

	public static class ContaExtensions
	{
	public static void MudaTitular(this Conta conta, this Cliente titular)
	{
	conta.Titular = titular;
	}
	}
	```

 O que podemos afirmar?

 * O código não compila, pois o this só pode ficar no primeiro argumento do extension method.

 * Compila normalmente e podemos usar o MudaTitular como extension method de Conta e de Cliente.

 * Compila normalmente, porém o método só pode ser usado como extension method de Conta.

 
1. Dadas as classes:

	``` csharp
	public abstract class Conta
	{
	public Cliente Titular { get; set; }
	// outros métodos e atributos da Conta
	}
	public static class ContaExtensions
	{
	public static void MudaTitular(this Conta c, Cliente titular)
	{
	c.Titular = titular;
	}
	}
	```

 O que podemos afirmar sobre o código a seguir?

	``` csharp
	Conta c = new ContaCorrente();
	Cliente titular = new Cliente("victor");
	ContaExtensions.MudaTitular(c, titular);
	```

 * Extension Method é um método estático comum e, portanto, o código do exercício funciona.

 * O código do exercício não compila. Só podemos usar o MudaTitular como extension method e não como método estático.

 * O código não compila, pois temos um this no primeiro argumento do MudaTitular.

 
