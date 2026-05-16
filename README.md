## Clase App

public class App {

    public static void main(String[] args) {

        Universidad uni = new Universidad("UNVIME");

        Estudiante e1 = new Estudiante("Ana", "A100");
        Estudiante e2 = new Estudiante("Juan", "B205");
        Estudiante e3 = new Estudiante("Lucas", "C310");
        Estudiante e4 = new Estudiante("Maria", "D415");
        Estudiante e5 = new Estudiante("Sofia", "E520");
        Estudiante e6 = new Estudiante("Pedro", "F625");

        uni.agregarEstudiante(e1);
        uni.agregarEstudiante(e2);
        uni.agregarEstudiante(e3);
        uni.agregarEstudiante(e4);
        uni.agregarEstudiante(e5);
        uni.agregarEstudiante(e6);

        uni.mostrarIndice();

        System.out.println("\nBUSQUEDA");

        Estudiante encontrado = uni.buscarEstudiante("C310");

        if (encontrado != null) {
            System.out.println("Encontrado: " + encontrado);
        } else {
            System.out.println("No encontrado");
        }
    }
}

## Clase IndiceEstudiantes

public class IndiceEstudiantes {

    private Estudiante[] tabla;
    private int tamaño;
    private float factorCargaMax;
    private int cantidadActual;

    public IndiceEstudiantes(int tamaño, float factorCargaMax) {
        this.tamaño = tamaño;
        this.factorCargaMax = factorCargaMax;
        this.cantidadActual = 0;

        tabla = new Estudiante[tamaño];

        for (int i = 0; i < tamaño; i++) {
            tabla[i] = null;
        }
    }

    // Función hash
    private int hash(String clave) {

        int suma = 0;

        for (int i = 0; i < clave.length(); i++) {
            suma += (int) clave.charAt(i);
        }

        return suma % tamaño;
    }

    // Insertar estudiante
    public void insertar(Estudiante e) {

        float factorCarga = (float) cantidadActual / tamaño;

        if (factorCarga >= factorCargaMax) {
            System.out.println("No se puede insertar. Factor de carga máximo alcanzado.");
            return;
        }

        String legajo = e.getLegajo();

        int posicionInicial = hash(legajo);

        int posicion = posicionInicial;

        int i = 1;

        // Exploración cuadrática
        while (tabla[posicion] != null) {

            posicion = (posicionInicial + (i * i)) % tamaño;
            i++;

            // seguridad para evitar loop infinito
            if (i == tamaño) {
                System.out.println("No hay posiciones disponibles.");
                return;
            }
        }

        tabla[posicion] = e;
        cantidadActual++;

        System.out.println("Estudiante " + legajo +
                " insertado en posición " + posicion);
    }

    // Buscar estudiante
    public Estudiante buscar(String legajo) {

        int posicionInicial = hash(legajo);

        int posicion = posicionInicial;

        int i = 1;

        while (tabla[posicion] != null) {

            if (tabla[posicion].getLegajo().equals(legajo)) {
                return tabla[posicion];
            }

            posicion = (posicionInicial + (i * i)) % tamaño;
            i++;

            if (i == tamaño) {
                return null;
            }
        }

        return null;
    }

    // Mostrar tabla completa
    public void mostrarTabla() {

        System.out.println("\n----- TABLA HASH -----");

        for (int i = 0; i < tamaño; i++) {

            System.out.print(i + " -> ");

            if (tabla[i] == null) {
                System.out.println("null");
            } else {
                System.out.println(tabla[i].getLegajo()
                        + " - "
                        + tabla[i].getNombre());
            }
        }
    }
}

## Clase Universidad

public class Universidad {

    private String nombre;
    private IndiceEstudiantes indice;

    public Universidad(String nombre) {
        this.nombre = nombre;

        indice = new IndiceEstudiantes(17, 0.7f);
    }

    public void agregarEstudiante(Estudiante e) {
        indice.insertar(e);
    }

    public Estudiante buscarEstudiante(String legajo) {
        return indice.buscar(legajo);
    }

    public void mostrarIndice() {
        indice.mostrarTabla();
    }
}

## Clase Estudiante

public class Estudiante {

    private String nombre;
    private String legajo;

    public Estudiante(String nombre, String legajo) {
        this.nombre = nombre;
        this.legajo = legajo;
    }

    public String getNombre() {
        return nombre;
    }

    public String getLegajo() {
        return legajo;
    }

    @Override
    public String toString() {
        return nombre + " - " + legajo;
    }
}

| Legajo | ASCII total | h(k) = ASCII % 17 | ¿Colisión? | Intentos (i²) | Posición final |
| ------ | ----------- | ----------------- | ---------- | ------------- | -------------- |
| A100   | 210         | 6                 | No         | —             | 6              |
| B205   | 221         | 0                 | No         | —             | 0              |
| C310   | 231         | 10                | No         | —             | 10             |
| D415   | 241         | 3                 | No         | —             | 3              |
| E520   | 242         | 4                 | No         | —             | 4              |
| F625   | 252         | 14                | No         | —             | 14             |

## ¿Dónde hubo más colisiones?

Las colisiones aparecen cuando distintos legajos producen el mismo valor hash. Esto sucede especialmente cuando los caracteres tienen sumas ASCII parecidas. En este caso no hubo colisiones.

## ¿Qué tan eficiente fue la exploración cuadrática?

Fue bastante eficiente porque evita agrupar elementos consecutivos como ocurre en el lineal. Las búsquedas siguen siendo rápidas mientras el factor de carga sea bajo.

## ¿Qué pasaría si el tamaño no fuera primo?

Si el tamaño de la tabla no es primo, la exploración cuadrática podría no recorrer todas las posiciones posibles de la tabla. Eso puede provocar ciclos y dejar espacios vacíos sin usar aunque todavía haya lugar disponible. Por eso normalmente se utilizan tamaños primos en tablas hash.

