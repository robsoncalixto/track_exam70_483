# Delegate 
Delegate é um objeto que pode ser apontado para um metódo, levando consideração que o número de parâmetros,os tipo de retorno e os parâmetros de entrada precisam ser identicos para delegates com tipo pré-definido. Resumidamente o delegate é uma instância do metódo que lhe foi atribuido.

### Criando um simples delegate

```csharp
void Main(){	
   	MeuDelegate del = Dobro;
   	int resultado = del(10);
   	Console.WriteLine(result); //20
}
static int Dobro(int x) => x * 2;
delegate int MeuDelegate(int x);
```
No exemplo acima o delegate **MeuDelegate** recebeu a instância do metódo **Dobro** e depois executa o metódo. A váriavel resultado está recendo uma soma executada pelo metódo **Dobro** que foi chamado pelo delegate, uma outra forma de executar o metódo atribuído seria utilizando o metódo **Invoke**.

## Passando um delegate por parâmetro
Podemos passar um delegate como parâmetro para um metódo. Chamamos o metódo que recebe um outro como parâmetro de higher-order function, na função que segue abaixo melhoraremos o primeiro exemplo passando um delegate como parâmetro.

```csharp
void Main()
{	
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

O metódo **Transforme** espera receber um array e um delegate. Observando bem o código acima percebemos que estamos passando o metódo **Dobro** como parâmetro e não o delegate. Veja que a atribuição ao delegate é feita no momento em que passamos o metódo **Dobro**.

## Diferença entre instância e static

Ao atribuir um metódo ao delegate podemos obter a informação de quem é sua instância e qual o metódo que lhe foi atribuído, entretanto, se o metódo foi static teremos uma referência apontando para **null**.
No exemplo abaixo vemos que um metódo static tem como referência **null**, já para uma instância temos quem é o objeto a quem pertece o metódo e sua referência.

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

----------------------------------------------------------------------------------------------------------------------------




## Multiplos metódos
Todo delegate pode receber um ou vários referências de metódos basta utilizar a combinação de operadores += para adicionar, a remoção é feita utilizando um conjunto de operadores também -=. 

### Exemplos 
Neste exemplo vamos imaginar que você criou um metódo que precisa realizar uma atividade demorada e é preciso um monitor para acompanhar.
```csharp
    public delegate void ProgressReporter (int percentComplete);
	 static void Main()	 {
		 ProgressReporter p = WriteProgressToConsole;
		 p += WriteProgressToFile;
		 Util.HardWork (p);
	 }
 	
	static void WriteProgressToConsole (int percentComplete) => Console.WriteLine (percentComplete);
 	static void WriteProgressToFile (int percentComplete) => Console.WriteLine ("Escrevendo em arquivo.");

	public class Util
	{
		 public static void HardWork (ProgressReporter p)
		 {
			 for (int i = 0; i <= 10; i++){
		 		p (i * 10); // Invoke delegate
		 		System.Threading.Thread.Sleep (100); // Simulate hard work
		 	}
		 }
	}


## Generic Delegate
Um delegate também pode ter tipo genérico como segue no exemplo :

```csharp
public delegate T Transformer<T> (T arg);
```

Criado o tio genérico podemos utiliza-lo para passar qualquer tipo como parâmetro ou retorno como segue o exemplo.

```csharp
 	public delegate T Transformer<T> (T arg);

	public class Util
	{
		 public static void Transform<T> (T[] values, Transformer<T> t) {
	 		for (int i = 0; i < values.Length; i++)
	 		values[i] = t (values[i]);
	 	}
	}
	static void Main() {
		int[] values = { 1, 2, 3 };
	 	Util.Transform (values, Square); // Hook in Square
	 	foreach (int i in values)
	 		Console.Write (i + " "); // 1 4 9
	}
	static int Square (int x) => x * x;
```

## Delegates Func e Action
Com delegates genéricos é possível escrever delegates que sejam capaz de trabalhar com qualquer tipo de metódo.

