# Métodos e atributos estáticos

Precisamos agora guardar a quantidade de contas corrente existentes no sistema. Uma das maneiras de fazer isso é gerenciar o total de contas e adicionar 1 unidade nesse total toda vez que criarmos uma nova conta:

``` csharp
int totalDeContasCorrente = 0;

// ...

ContaCorrente novaConta = new ContaCorrente();
totalDeContasCorrente++;
```

Contudo, utilizando essa abordagem, um desenvolvedor pode esquecer de alterar a variável `totalDeContasCorrente` após criar uma nova `ContaCorrente`, gerando um erro no sistema. Para evitar isso, seria melhor que a própria classe controlasse o total de contas criadas. Uma primeira ideia seria guardar um atributo com o total de contas criadas na classe e, no seu próprio construtor, adicionar uma unidade nesse atributo:

``` csharp
public class ContaCorrente : Conta
{
	// Outros atributos da classe
	private int totalDeContas = 0;

	public ContaCorrente()
	{
		this.totalDeContas++;
	}

	// Métodos da classe
}
```

Qual seria o valor do atributo `totalDeContas` se fossem criadas duas contas?

``` csharp
ContaCorrente primeira = new ContaCorrente();
ContaCorrente segunda = new ContaCorrente();
```

Ambas as contas apresentariam o valor 1 no seu atributo `totalDeContas`. Isso acontece porque o atributo `totalDeContas` é diferente para cada objeto que instanciamos, isto é, o atributo pertence a cada objeto.

O que desejamos é que que tivéssemos um atributo compartilhado em todos os objetos da classe, ou seja, que o atributo pertença à classe ao invés dos objetos.

Estes atributos recebem o nome de atributos da classe ou atributos estáticos e, em C#, para criar um atributo estático basta colocar a palavra **static** na declaração do atributo:

``` csharp
public class ContaCorrente : Conta
{
	private static int totalDeContas = 0;
        // resto do código existente
}
```

Com isso, o nosso construtor ficaria:

``` csharp
public class ContaCorrente : Conta
{
	private static int totalDeContas = 0;
    public ContaCorrente
    {
    	    ContaCorrente.totalDeContas++;
    }
        // resto do código existente
}
```

O próximo passo é criar um controle que mostra qual o número da próxima conta disponível, isto é, o total de contas mais um. Com isso, criaríamos um método público que devolva o `totalDeContas + 1`:

``` csharp
public class ContaCorrente : Conta
{
	private static int totalDeContas = 0;
    public ContaCorrente
    {
    	    ContaCorrente.totalDeContas++;
    }
	public int ProximaConta()
	{
		return ContaCorrente.totalDeContas + 1;
	}
        // resto do código existente
}
```

Mas como o método pertence ao objeto (não é estático) e não à classe, temos que instanciar uma conta para poder acessá-lo:

``` csharp
// aqui o total é 0, imprimiria 1, que desejamos

ContaCorrente conta = new ContaCorrente();
conta.ProximaConta(); // imprime 2, pois já criamos uma
```

Perceba que precisamos criar um novo objeto para chamar o método `ProximaConta`. Mas, ao criar uma nova conta, o valor do `totalDeContas` já foi alterado. Para evitar isso, o método precisa pertencer à classe ao invés do objeto. De maneira semelhante a um atributo estático, colocando a palavra `static` ao declarar um método, este se torna estático:

``` csharp
public class ContaCorrente : Conta
{
       // resto do código existente

	public static int ProximaConta()
	{
		return ContaCorrente.totalDeContas + 1;
	}
}
```

E para usar esse novo método:

``` csharp
int proxima = ContaCorrente.ProximaConta();
```

## Exercícios Opcionais
1. No cadastro de contas, estamos pedindo o número que será cadastrado na nova conta, mas em nosso banco, duas contas não podem ter o mesmo número. Para garantirmos que o número das contas será único, podemos utilizar o `static` do C# para criar um contador de instâncias de contas que foram criadas.

	Declare na classe conta um novo atributo estático chamado `numeroDeContas` que contará quantas contas foram criadas na aplicação. Adicione um construtor na class `Conta` que não recebe nenhum argumento e gera o `Numero` utilizando o valor do atributo estático `numeroDeContas`:

	``` csharp
 public abstract class Conta
 {
    private static int numeroDeContas;

    public Conta()
    {
        Conta.numeroDeContas++;
        this.Numero = Conta.numeroDeContas;
    }

    // Resto da classe continua igual
 }
	```
1. Abra o formulário de cadastro de novas contas. Procure no código desse formulário a ação do botão que cadastra a nova conta utilizando os dados digitados pelo usuário, método `botaoCadastro_Click`. Dentro desse método, apague a linha que atribui o número da conta que será criada. Esse número agora é gerado pela própria classe conta.
1. Agora que a conta gera seu número, não é mais possível para o usuário saber qual será o número da conta que será cadastrada. Para resolvermos esse problema, vamos mostrar qual será o número da próxima conta no campo `textoNumero` do formulário de cadastro.

	O `numeroDeContas` é um atributo estático e privado dentro da classe `Conta`, logo o formulário não pode acessar o valor desse atributo para mostrá-lo. Crie um novo método estático na classe `Conta` chamado `ProximoNumero` que será responsável por devolver o número da próxima Conta que será criada pela aplicação.

    Como o valor do atributo `numeroDeContas` é incrementado apenas quando o construtor da `Conta` é executado, para exibir qual será o próximo número da conta que será criada, retorne o `numeroDeContas` + 1:

    ```csharp
    public static int ProximoNumero()
    {
        return numeroDeContas + 1;
    }
    ```

1. Agora vamos fazer o formulário de cadastro mostrar o número da próxima conta para o usuário. Dê um duplo clique no formulário de cadastro para fazer com que o Visual Studio crie o método que será executado no load do formulário. Dentro desse método, mostre o resultado do método `Conta.ProximoNumero()` no campo `textoNumero`.

	``` csharp
 private void FormCadastroConta_Load(object sender, EventArgs e)
 {
    textoNumero.Text = Convert.ToString(Conta.ProximoNumero());
 }
	```



## Para saber mais classes estáticas

Algumas vezes criamos classes que contêm apenas métodos auxiliares estáticos. Como essas classes não possuem métodos nem propriedades de instâncias, não queremos permitir que elas sejam instanciadas. Nessas situações, podemos utilizar as classes estáticas do C#. Para criar uma classe estática, precisamos apenas utilizar a palavra `static` em sua declaração:

``` csharp
public static class Funções 
{
    // implementação
}
```

Quando uma classe é declarada como estática, ela não pode ser instanciada e nem herdada e, portanto, só pode possuir membros estáticos.

``` csharp
public static class Funcoes 
{
    // Esse método é válido dentro de uma classe estática.
    public static bool MetodoEstatico()
    {
        // implementação
    }
    
    // Esse método não é válido dentro de uma classe estática.
    public bool MetodoInstancia()
    {
        // implementação
    }
}
```
