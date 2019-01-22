# Delegate 

Delegate é um objeto que pode chamar um metódo, desde que o tipo de retorno e  
parâmetros  de entrada seja identicos é possível que um metódo seja chamado pelo delegate.
Pode-se dizer que um delegate tem um referência do metógo que lhe foi atribuído como no exemplo abaixo.

## exemplo simples 
```csharp
void Main(){	
    	transformar t = soma;	
    	int result = t(10,10);	
    	Console.WriteLine(result);
}
static int soma(int x, int y) => x + y;
delegate int transformar(int x, int y);
```
Podemos receber uma delegate como função, assim como um metódo. Chamamos o metódo que recebe um outro como parâmetro de higher-order function.
```csharp
void Main()
{	
	int[] values = {1,2,3,58};
	Util.Transform (values, Square);
	foreach( int i in values)
		Console.WriteLine(i + " ");

}

static int Square(int x ) => x * x;

public delegate int Transformer(int x);

public class Util {
	public static void Transform(int[] values, Transformer t){
			for(int i =0; i< values.Length; i++)
			    values[i]= t(values[i]);
	}
}
```
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
```
Interessante também observar que é possível saber a qual instância pertece o delate,
no exemplo acima a referência para o delegate será null, pois, trata-se de um metódo static, porém, se tivermos um objeto delegate recebendo um metódo de algum objeto que tenha sido instanciado podemos obter quem é este objeto.
```csharp
class Test
{
 static void Main()
 {
 X x = new X();
 ProgressReporter p = x.InstanceProgress;
 p(99); // 99
 Console.WriteLine (p.Target == x); // True
 Console.WriteLine (p.Method); // Void InstanceProgress(Int32)
 }
}
class X
{
 public void InstanceProgress (int percentComplete)
 => Console.WriteLine (percentComplete);
}
```
