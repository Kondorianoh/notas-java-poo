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

## Colecciones: `List` y `ArrayList`
Las colecciones permiten administrar varios objetos como una unidad.

### `List<T>` (La interfaz):
* Define la idea **general** de una lista y las operaciones que tiene una lista ordenada (`add`, `get`, `remove`, `size`, etc.).
* No se puede instanciar por sí misma, ya que una interfaz define el contrato pero no representa la construcción concreta.

### `ArrayList<T>` (La clase):
* Implementa `List` usando un array redimensionable que puede ampliar su capacidad cuando necesita más espacio.
* Puede instanciarse con `new ArrayList<>()`, lo que dá una forma concreta de construir y administrar unna lista.
* Es ordenada, permite elementos repetidos y el accedo por índice.

```java
List<Empleado> nomina = new ArrayList<>();
```

### Referencia
Con `nomina.add(new EmpleadoPlanta(...))`, por ejemplo, el `ArrayList` no guarda el objeto completo. Lo que hace es guardar una referencia (dirección de memoria) que apunta al lugar de la memoria RAM en el que se encuentra ese objeto.

Los objetos existen de manera independiente, por lo que una misma lista puede apuntar a instancias de distintas clases hija de Empleado.

```java
List<Empleado> nomina = new ArrayList<>();

nomina.add(new EmpleadoPlanta("E01", "Pepa Pig", 950000));
nomina.add(new Pasante("E02", "Pepe Argento", 80, 4500));

for (Empleado empleado : nomina) {
    empleado.mostrarDatos();
    System.out.println(empleado.calcularSueldo());
}
```

La lista se declara con el tipo general (`Empleado`). Cada posición puede referenciar una clase hija diferente y el recorrido utiliza el mismo mensaje para todas:

* La lista toma cualquier objeto que sea un `Empleado`.

* La variable del `for` conserva el tipo general.

* Todos reciben el mismo mensaje (ej. calcularSueldo()).

* Cada objeto ejecuta su propia fórmula.

## Interfaces
Es un contrato que define qué operaciones tiene que realizar un componente, pero no dice cómo las hace. Es decir, no puede ser implementada por sí misma, sino que otras clases la heredan y la implementan. Se declara usando la palabra clave `interface`.

* No se puede instanciar.
* Sus métodos son **abstractos**.
* Una clase puede implementar varias interfaces.
* Las interfaces pueden implementar otras interfaces.

```java
public interface VehiculoServicio {

    boolean agregarVehiculo(Vehiculo vehiculo);

    Vehiculo buscarPorPatente(String patente);

    boolean eliminarPorPatente(String patente);

    void listarVehiculos();
}
```

Una clase se compromete a cumplir ese contrato con la palabra clave `implements`  y escribiendo el código de cada método con el `@Override`

```java
import java.util.ArrayList;
import java.util.List;

public class VehiculoServicioMemoria implements VehiculoServicio {

    private List<Vehiculo> vehiculos;

    public VehiculoServicioMemoria() {
        vehiculos = new ArrayList<Vehiculo>();
    }

    @Override
    public boolean agregarVehiculo(Vehiculo vehiculo) {

        return vehiculos.add(vehiculo);
    }

    @Override
    public Vehiculo buscarPorPatente(String patente) {

        for (Vehiculo vehiculo : vehiculos) {

            if (vehiculo.getPatente().equalsIgnoreCase(patente)) {

                return vehiculo;
            }
        }

        return null;
    }

    @Override
    public boolean eliminarPorPatente(String patente) {

        Vehiculo encontrado = buscarPorPatente(patente);

        if (encontrado == null) {
            return false;
        }

        return vehiculos.remove(encontrado);
    }

    @Override
    public void listarVehiculos() {

        for (Vehiculo vehiculo : vehiculos) {
            System.out.println(vehiculo.mostrarInformacion());
        }
    }
}
```

De esta forma el resto del programa sólo conoce el contrato (`VehiculoServicio`). Si un día querés cambiar a una versión con Base de Datos o que trabaje con un archivo, sólo hay que cambiar la línea donde se instancia, y el resto del programa sigue funcionando exactamente igual.

---
# Manejo de Archivos

Permite guardar información en el almacenamiento del dispositivo para poder recuperarla después.

A diferencia de una variable o una colección como `ArrayList`, cuyos datos se mantienen en la memoria RAM durante la ejecución del programa, un archivo permite conservar información de forma persistente. Esto quiere decir que los datos guardados se mantienen después de cerrar el programa.

## Clase `File`

La clase File, del paquete java.io, representa una ruta de archivo o de carpeta. Te deja consultar si existe, obtener información de su ubicación y crear carpetas, entre otras cosas.

```java
import java.io.File;

File archivo = new File("datos/cosas.txt");
```

En este caso, `archivo` representa la ruta `datos/cosas.txt` (en la carpeta `datos` está el archivo `cosas.txt`).
Por ejemplo, si tenés una carpeta adentro de otra carpeta y esa carpeta contiene un archivo, la ruta sería así: `carpeta1/carpeta2/archivo.txt`.

**Importante:** Instanciar un objeto `File` no crea físicamente el archivo: solamente representa su ubicación.

### Métodos importantes:

* **exists():** muestra si el archivo o carpeta existe. Devuelve un booleano.
* **getName():** devuelve el nombre del archivo o carpeta.
* **getAbsolutePath():** devuelve la **ruta absoluta**.

Una ruta absoluta es la dirección **completa** y **exacta** de un archivo o carpeta en tu sistema, comenzando desde la raíz de almacenamiento de tu computadora.

* **getParentFile():** devuelve un objeto `File` que representa la carpeta contenedora.
* **mkdir():** crea una carpeta.
* **mkdirs():** crea las carpetas necesarias de una ruta, incluidas las intermedias.

Por ejemplo, si querés crear la carpeta donde se va a guardar un archivo:

```java
public static void crearCarpeta(String rutaDeArchivo) {

    File archivo = new File(rutaDeArchivo);

    File carpeta = archivo.getParentFile();

    if (carpeta != null && !carpeta.exists()) {

        boolean creada = carpeta.mkdirs();

        if (creada) {
            System.out.println("Carpeta creada: " + carpeta.getAbsolutePath());
        }
    }
}
```

1. Crea un objeto `File` que representa la ruta del archivo.
2. Con `getParentFile()` se obtiene la carpeta que lo va a contener.
3. Verifica que la carpeta no sea `null` y que todavía **no exista**.
4. Con mkdirs() intenta crearla.

La comprobación `carpeta != null` evita intentar trabajar con una carpeta inexistente como referencia, por ejemplo, cuando la ruta no contiene una carpeta padre explícita.

## Escritura de archivos: `FileWriter` y `BufferedWriter`

Para escribir texto en un archivo se puede usar `FileWriter`. Si se combina con `BufferedWriter`, se puede escribir de manera más cómoda y eficiente, con métodos como `write()` y `newLine()`.

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

try (BufferedWriter bw = new BufferedWriter(new FileWriter("datos/cosas.txt"))) {

    bw.write("Al mazo trampiao voy a torcerle un destino");
    bw.newLine();
    bw.write("Levántate, cagón que aquí canta un argentino!");

} catch (IOException e) {
    System.out.println(e.getMessage());
}
```

* **FileWriter:** permite escribir caracteres en un archivo.
* **BufferedWriter:** optimiza el rendimiento y la velocidad del programa al escribir datos en el disco.

Si solo usás `FileWriter`, el programa va a enviar cada caracter, palabra o línea directamente al disco duro de la computadora en el momento de la ejecución del código. Como el acceso al disco es una de las tareas más lentas en informática, hacer eso satura el sistema y vuelve al programa muy lento.

`BufferedWriter` actúa como contenedor temporal en la memoria RAM (el "buffer").

1. Cuando se ejecuta `bw.write("Pepa Pig")`, el texto no va al archivo; se guarda en la memoria RAM de manera instantánea.
2. Sigue acumulando texto en la RAM a medida que seguís escribiendo.
3. Cuando ese contenedor se llena (o cuando vos quieras), `BufferedWriter` agarra todo ese bloque grande de datos y lo guarda en el disco duro de una sola vvez.


* **write("texto"):** escribe el texto que se le da.
* **newLine():** agrega un salto de línea adecuado para el sistema operativo.
* **IOException:** representa posibles errores de entrada o salida, por ejemplo, problemas de permisos o de acceso al archivo.

### TRY-CATCH

Es una estructura de Java que permite abrir recursos y cerrarlos automáticamente al finalizar el bloque, incluso si ocurre una excepción.

```java
try (BufferedWriter bw = new BufferedWriter(new FileWriter("datos/cosas.txt"))) {

    // Operaciones de escritura.

} catch (IOException e) {
    System.out.println(e.getMessage());
}
```

No hace falta llamar manualmente a `bw.close()`, porque Java lo hace automáticamente.

**Importante:** cuando se usa `new FileWriter(ruta)` si el archivo ya existe, su contenido se reemplaza. Si querés agregar texto al final sin borrar lo anterior hay que agregar un `true` como segundo argumento:

```java
new FileWriter("datos/cosas.txt", true)
```

El segundo argumento `true` activa el modo de agregado (append).

## Lectura de archivos: `FileReader` y `BufferedReader`

Para leer un archivo de texto se puede usar `FileReader` junto con `BufferedReader`.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

try (BufferedReader br = new BufferedReader(new FileReader("datos/cosas.txt"))) {

    String linea = br.readLine();

    while (linea != null) {

        System.out.println(linea);

        linea = br.readLine();
    }

} catch (IOException e) {
    System.out.println(e.getMessage());
}
```

* **FileReader:** permite leer caracteres desde un archivo.
* **BufferedReader:** facilita la lectura de texto mediante un búfer.
* **readLine():** lee una línea y devuelve un `String`. Si no quedan más líneas, devuelve `null`.
* **while (linea != null):** permite procesar las líneas hasta llegar al final del archivo.

La variable `linea` guarda el contenido de una línea en cada iteración del `while`. Después de procesarla, se llama otra vez a `readLine()` para que avence a la siguiente.

Es importante actualizar `linea` adentro del bucle porque, si su valor no cambia, el programa puede quedarse ejecutando el bucle infinitamente.

## Guardar y leer una lista de líneas

Se puede combinar el **manejo de archivos** con las colecciones. Por ejemplo, una lista de tipo `List<String>` permite almacenar varias cadenas de caracteres y escribir cada una en una línea del archivo.

**Primero, se guardan las líneas:**

```java
public static void guardarLineas(List<String> lineas, String rutaDeArchivo) {

    crearCarpeta(rutaDeArchivo);

    try (BufferedWriter bw = new BufferedWriter(new FileWriter(rutaDeArchivo))) {

        for (String linea : lineas) {

            bw.write(linea);
            bw.newLine();
        }

        System.out.println("Archivo guardado.");

    } catch (IOException e) {
        System.out.println(e.getMessage());
    }
}
```

**Después, se recuperan esas líneas:**

```java
public static List<String> leerLineas(String rutaDeArchivo) {

    List<String> lineas = new ArrayList<String>();

    try (BufferedReader br = new BufferedReader(new FileReader(rutaDeArchivo))) {

        String linea = br.readLine();

        while (linea != null) {

            lineas.add(linea);
            linea = br.readLine();
        }

    } catch (IOException e) {
        System.out.println(e.getMessage());
    }

    return lineas;
}
```

En `guardarLineas()`:

1. Llama a la función `crearCarpeta()` para asegurar de que exista la carpeta contenedora.
2. Abre el archivo con `FileWriter`.
3. Recorre la lista con un `for-each`.
4. Escribe cada cadena y agrega un salto de línea.
5. Cierra automáticamente el recurso cuando sale del `try`.

En `leerLineas()`:

1. Crea un `ArrayList<String>` vacío.
2. Abre el archivo con `FileReader` y `BufferedReader`.
3. Lee una línea.
4. Mientras la línea no sea `null`, la agrega a la lista.
5. Devuelve la lista completa con el `return`.

Si hay un error durante la lectura, imprime el mensaje de la excepción y el método devuelve la lista que haya podido recuperar. Si no pudo leer ninguna línea, devuelve una lista vacía.

### Programa completo

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class Main {

    public static void main(String[] args) {

        String rutaDeArchivo = "datos/cosas.txt";

        List<String> lineas = new ArrayList<String>();

        lineas.add("Al mazo trampiao voy a torcerle un destino");
        lineas.add("Levántate, cagón que aquí canta un argentino!");

        guardarLineas(lineas, rutaDeArchivo);

        lineas = leerLineas(rutaDeArchivo);

        System.out.println(lineas);
    }

    public static void crearCarpeta(String rutaDeArchivo) {

        File archivo = new File(rutaDeArchivo);
        File carpeta = archivo.getParentFile();

        if (carpeta != null && !carpeta.exists()) {

            boolean creada = carpeta.mkdirs();

            if (creada) {
                System.out.println("Carpeta creada: " + carpeta.getAbsolutePath());
            }
        }
    }

    public static void guardarLineas(List<String> lineas, String rutaDeArchivo) {

        crearCarpeta(rutaDeArchivo);

        try (BufferedWriter bw =
                new BufferedWriter(new FileWriter(rutaDeArchivo))) {

            for (String linea : lineas) {
                bw.write(linea);
                bw.newLine();
            }

            System.out.println("Archivo guardado.");

        } catch (IOException e) {
            System.out.println(e.getMessage());
        }
    }

    public static List<String> leerLineas(String rutaDeArchivo) {

        List<String> lineas = new ArrayList<String>();

        try (BufferedReader br = new BufferedReader(new FileReader(rutaDeArchivo))) {

            String linea = br.readLine();

            while (linea != null) {
                lineas.add(linea);
                linea = br.readLine();
            }

        } catch (IOException e) {
            System.out.println(e.getMessage());
        }

        return lineas;
    }
}
```
