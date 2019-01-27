# Delegate 
Delegate é um objeto que pode conter a referência de um método, levando consideração que o número de parâmetros,os tipo de retorno e os parâmetros de entrada precisam ser identicos para Delegates com tipo pré-definido. Resumidamente o Delegate é uma instância do método que lhe foi atribuido e sua execução será em uma thread diferente da thread de excução no momento. Delegates são ótimas opções para evitar problemas com Cross-Threading que geralmente acontece quando tenta acessar um método ou propriedade de outra.

### Criando um simples Delegate

```csharp
	void Main(){	
		MeuDelegate del = Dobro;
		int resultado = del(10);
		Console.WriteLine(result); //20
	}
	static int Dobro(int x) => x * 2;
	Delegate int MeuDelegate(int x);
```

No exemplo acima o Delegate **MeuDelegate** recebeu a instância do método **Dobro** e depois executa o método. A váriavel resultado está recendo uma soma executada pelo método **Dobro** que foi chamado pelo Delegate, uma outra forma de executar o método atribuído seria utilizando o método **Invoke**.

## Passando um Delegate por parâmetro
Podemos passar um Delegate como parâmetro para um método. Chamamos o método que recebe um outro como parâmetro de higher-order function, na função que segue abaixo melhoraremos o primeiro exemplo passando um Delegate como parâmetro.

```csharp
	void Main(){	
		int[] valores = {1,2,3};

		Util.Transforme (values, Dobro);
		foreach( int i in valores)
			Console.WriteLine(i + " "); //2 4 6
	}

	static int Dobro(int x ) => x * 2;

	public delegate int meuDelegate(int x);

	public class Util {
		public static void Transforme(int[] valores, meuDelegate del){
			for(int i =0; i< valores.Length; i++)
				valores[i]= del(valores[i]);
		}
	}
```

O método **Transforme** espera receber um array e um Delegate . Observando bem o código acima percebemos que estamos passando o método **Dobro** como parâmetro e não o Delegate . Veja que a atribuição ao Delegate  é feita no momento em que passamos o método **Dobro**.

## Diferença entre instância e static
Ao atribuir um método ao Delegate  podemos obter a informação de quem é sua instância e qual o método que lhe foi atribuído, entretanto, se o método foi static teremos uma referência apontando para **null**.
No exemplo abaixo vemos que um método static tem como referência **null**, já para uma instância temos quem é o objeto a quem pertece o método e sua referência.

```csharp
	public delegate void MeuDelegate();
	
	static void Main()
	{
		MinhaClasse m = new MinhaClasse();
		MeuDelegate del = m.Metodo2;			
		Console.WriteLine (del.Target == m); // True
		Console.WriteLine (del.Target); // MinhaClasse
		Console.WriteLine (del.Method); // Void Metodo2()
		
		del = MinhaClasse.Metodo1;
		Console.WriteLine (del.Target == m); // False
		Console.WriteLine (del.Target ); // null 
		Console.WriteLine (del.Method); // Void Metodo2()
	}

	public class MinhaClasse {
        public static void Metodo1() => Console.WriteLine("Metodo 1");
		public void Metodo2() => Console.WriteLine("Metodo 2");
	}
```

## Multicast Delegate
Os Delegates tem a possibilidade de adicionar mais de um método como referência, porém, a execução é de acordo com a ordem ao qual foi adicionada os métodos. Seguindo o padrão que já analisamos anteriormente de tipo de dados de assinatura. No exemplo a seguir veremos como trabalhar com mais de um método.

```csharp 
	delegate int MeuDelegate();
	void Main()
	{
		MeuDelegate del = RetornaDez;
		del += RetornaCem;
		del += RetornaMil;
		Console.WriteLine(del()); // 1000
		del -= RetornaMil;
		Console.WriteLine(del()); // 100
		del += RetornaMil;
		
		foreach(int valor in RetornaTodosOsDelegates(del)) // 10 100 1000
			Console.WriteLine(valor);
	}

	static List<int> RetornaTodosOsDelegates(MeuDelegate del){
		List<int> valores = new List<int>();
		foreach(MeuDelegate d in del.GetInvocationList())
			valores.Add(d());
		return valores;
	}
```

No código de exemplo observamos que os operadores **+=** faz atribuição do método ao Delegat, já os operadores **-+** subtrair da pilha de execução do Delegate  o método. Podemos remover qualquer método pelo seu nome, mas ao executar sempre executamos o último que foi adicionado na pilha. Para execução de todos os métodos utilizamos o método **GetInvocationList** do objeto Delegate  que nos permite fazer um loop em todos os métodos da da pilha.

## Func, Action e Predicate
O .net tem alguns Delegates prontos para facilitar que não tenhamos a necessidade de criar para algumas atividades. Iremos analisar o uso dos Delegates **Function, Action e Predicate**.

- **Func**
O Delegate Func nos permite trabalhar com tipos de dados de retorno nos dand uma imensa possibilidade de aplicação. 

```csharp
	void Main()
	{
		Func<string, int, bool> MinhaFunc = StringEIntRetornandoBoleano; 
		if(MinhaFunc("Robson",25))
			Console.WriteLine("Func executado com sucesso!");
	}

	static bool StringEIntRetornandoBoleano(string nome, int idade){
		Console.WriteLine($"Nome : {nome} , idade : {idade}");
		return true;
	}
```

- **Action**
O Delegate Action encapsula um método void, um método que não retorna nada.

```csharp
	void Main(){

		string[] nomes = new string[] {"Robson","Thiago","Pietro"};
		
		Action<string> acao = new Action<string>(Console.WriteLine);
		Array.ForEach(nomes, acao);
		
		//Chamando um novo metodo
		string[] nomesCompletos = new string[] {"Robson Calixto","Thiago Pereira","Pietro Silva"};
		acao = ImprimirNomeNoConsole;
		Array.ForEach(nomesCompletos, ImprimirNomeNoConsole);
		
		Console.WriteLine("Action executado com sucesso!");
	}
	static void ImprimirNomeNoConsole(string mensagem) => Console.WriteLine(mensagem);
```

- **Predicate**
O Delegate Predicate nos permite passar apenas um parâmetro e retorna um boleano diferente do Func que retorna o tipo que desejar e Action que é um void.

```csharp
	void Main()
	{
		//Expressão lambda
		Predicate<int> acao = objetoPredicate => true;
		if(acao(25)) // retorna sempre true
			Console.WriteLine("Novinho esse garoto!");
		
		//Metódo anônimo
		Predicate<int> acao2 = objetoPredicate => true;
		if(acao(25)) // retorna sempre true
			Console.WriteLine("Continua novinho esse garoto!");
		
		//Método explicito
		Predicate<int> acao3 = EhNovinho;
		if(acao3(31))  
			Console.WriteLine("É novinho!"); 
		else 
			Console.WriteLine("Não é novinho!");
	}	
	static bool EhNovinho(int idade) {	
		return idade <= 30 ? true : false;
	}
```