# Notas de clase (POO)

## Herencia
La herencia permite a una clase (subclase o clase hija) reutilizar, extender o modificar los atributos y métodos de otra clase (superclase o clase padre). En términos más simples, una clase hija hereda los atributos y métodos de una clase padre, y se le añaden sus propios atributos y métodos específicos.

En Java se utiliza la palabra clave `extends`.

Clase padre:
```java
public class Animal {
    private String nombre;

    public void comer() {
        System.out.println("El animal está comiendo.");
    }
}
```

Se puede crear una clase hija `Perro` que herede de `Animal`:
```java
public class Perro extends Animal {
    public void ladrar() {
        System.out.println("Guau!");
    }
}
```

Ahora `Perro` tiene sus propios métodos y, además, los heredados.

### Ventajas de la herencia
* Permite reutilizar código.
* Evita duplicación.
* Facilita la creación de jerarquías de clases.
* Permite implementar polimorfismo.

### Generalización y Especialización
* **Generalización:** Es el proceso de extraer características comunes de varias clases para crear una clase superior (superclase o clase padre). Por ejemplo si tenés `Perro` y `Gato`, la generalización hace que crees una clase padre `Animal`. O si tenés `EmpleadoPlanta` y `Pasante`, creás una clase padre `Empleado`.

Como en la vida real, las cosas se agrupan por similitudes. Un `Perro` y un `Gato` comparten atributos como `nombre` o `edad`, y comportamientos como `comer()` o `dormir()` porque los dos son animales.

Una buena forma de saber si la herencia tiene sentido es usando la relación "Es un/a" (Is-a). Por ejemplo: El perro **es un** animal. El Pasante **es un** empleado.

* **Especialización:** Es el proceso inverso a la generalización. A partir de una clase general se crean clases derivadas que añaden atributos y comportamientos específicos, es decir, consiste en crear nuevas clases hijas a partir de una clase padre existente. Por ejemplo, la clase `Perro` hereda de `Animal` y añade el método `ladrar()`. O la clase `Pasante` hereda de `Empleado` y añade los atributos `horas` y `valorPorHora`.

## Clases Abstractas
Son clases que no se pueden instanciar directamente. Sirven como plantillas base para otras clases. Se declaran usando la palabra clave `abstract`.

- Pueden tener atributos, constructor y métodos concretos (con código).

- Pueden tener métodos abstractos (sin código), que obligatoriamente tienen que ser implementados por cualquier clase concreta que herede.

```java
// Generalización.
public abstract class Empleado {
	private String legajo;
	private String nombre;

	public Empleado() {
	}

	public Empleado(String legajo, String nombre) {
		this.legajo = legajo;
		this.nombre = nombre;
	}

    // Método abstracto: obliga a las hijas a implementarlo a su manera.
    // No tiene cuerpo entre llaves. Se pone un punto y coma al final.
	public abstract double calcularSueldo();

    // Metódo concreto.
	public void mostrarDatos() {
		System.out.println("Legajo: " + legajo + " Nombre: " + nombre + " Sueldo: " + calcularSueldo());
	}

}
```

```java
// Especialización.
public class EmpleadoPlanta extends Empleado {
    private double sueldoMensual;

    public EmpleadoPlanta(String legajo, String nombre, double sueldoMensual) {
        super(legajo, nombre); // El super inicializa los datos del padre
        this.sueldoMensual = sueldoMensual;
    }

    // El @Override indica que se está sobrescribiendo un método hererdado.
    @Override 
    public double calcularSueldo() {
        return sueldoMensual;
    }
}
```

```java
// Especialización.
public class Pasante extends Empleado {
    private int horas;
    private double valorHora;

    public Pasante(String legajo, String nombre, int horas, double valorHora) {
        super(legajo, nombre);
        this.horas = horas;
        this.valorHora = valorHora;
    }

    @Override
    public double calcularSueldo() {
        return horas * valorHora;
    }
}
```

### Tipo vs Instancia
```java
Empleado e1 = new EmpleadoPlanta(...)
```

* **Tipo (`Empleado`):** Es la declaración de la variable. Para Java `e1` es del tipo `Empleado`. Esto significa que sólo se pueden usar los métodos y atributos que conoce la clase `Empleado`.
* **Instancia (`new EmpleadoPlanta(...)`):** Es el objeto real que se guarda en la memoria RAM durante la ejecución del programa.

Entonces, el tipo de la variable puede ser general(`Empleado`) y la instancia puede ser específica (`EmpleadoPlanta`). Esto es importante para el **principio de sustitución**.


### Principio de Sustitución y Polimorfismo
Establece que un objeto de una clase hija puede ser utilizado en cualquier lugar donde se espere un objeto de la clase padre, sin que el programa se rompa o se comporte de manera inesperada. En criollo: podés tratar a cualquier clase hija como si fuera su clase padre.

Cuando se ejecuta un método a través de una variable de tipo general, Java decide en tiempo de ejecución qué versión del método invocar basándose en el objeto real (la instancia). Esta capacidad de que una misma llamada produzca diferentes comportamientos según el objeto es lo que se conoce como **polimorfismo**, y al mecanismo técnico que lo hace posible se le llama **despacho dinámico**.

1. La variable `e1` es de tipo Empleado.

2. Llama al método `e1.calcularSueldo()`.

3. Java detecta que el objeto real es un EmpleadoPlanta y ejecuta automáticamente la versión `calcularSueldo()` propia de esa clase hija.

```java
// Sustitución y Despacho Dinámico.
public class Main {
    public static void main(String[] args) {
        // Variables de tipo general (Empleado) apuntando a objetos específicos
        Empleado empleado1 = new EmpleadoPlanta("E01", "Pepa Pig", 950000);
        Empleado empleado2 = new Pasante("E02", "Pepe Argento", 80, 4500);

        // Polimorfismo: el sistema sabe qué método ejecutar en cada caso
        empleado1.mostrarDatos(); // Legajo: E01 | Nombre: Pepa Pig | Sueldo: $950000.0
        empleado2.mostrarDatos(); // Legajo: E02 | Nombre: Pepe Argento | Sueldo: $360000.0
    }
}
```

Todos los empleados tienen un método llamado `calcularSueldo()`.

* Como el tipo de la variable es `Empleado`, podés tener una lista con muchos empleados diferentes (`EmpleadoPlanta`, `Pasante`, `Gerente`, etc).

* Podés recorrer esa lista con un bucle y calcular el sueldo de cada uno.

* Gracias al despacho dinámico (polimorfismo), el sistema no se queda con la regla general de la clase padre `Empleado`, sino que va a buscar la implementación específica del objeto real que está en la memoria.